# 🚀 ESPHome Installation & Flashing Guide  
### For LilyGO T-CAN485 & Marstek Battery Integration

This guide explains how to install ESPHome on **Windows or Linux** and flash it onto a **LilyGO T-CAN485** to communicate with **Marstek RS485 batteries** using a custom ESPHome configuration.

---

## 🛠️ Requirements

- Python 3.8 or higher (on Windows or Linux)
- USB-C cable (must support data transfer)
- LilyGO T-CAN485 development board
- ESPHome YAML configuration file (`Marstek-Lilygo-ESPHome-Config.yaml`)
- Internet connection (for dependency installation)
- **USB Drivers** (Windows only - see below)

---

## 🔌 USB Driver Installation (Windows Only)

The LilyGO T-CAN485 uses a **CH9102** or **CP2104** USB-to-Serial chip. Install the appropriate driver:

### CH9102 Driver (most common)
- **Download**: [CH9102 Driver (WCH Official)](https://www.wch.cn/downloads/CH343SER_EXE.html)
- Alternative: [CH341SER Driver](https://www.wch.cn/downloads/CH341SER_EXE.html)

### CP210x Driver (alternative)
- **Download**: [Silicon Labs CP210x Driver](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers)

**Installation Steps:**
1. Download and run the driver installer
2. Restart your PC
3. Connect the LilyGO board via USB-C
4. Check Device Manager → Ports (COM & LPT)
5. You should see a new COM port (e.g., COM3, COM5)

> 💡 **Linux users**: Drivers are usually included in the kernel. No installation needed.

---

## 💻 ESPHome Installation

### ✅ Windows

Open PowerShell or CMD:

    # Step 1: Upgrade pip
    python -m pip install --upgrade pip

    # Step 2: Install ESPHome
    pip3 install esphome --upgrade

    # Step 3: Verify installation
    esphome version

**Expected output:** `Version: 2025.12.x`

> ✅ **Note**: ESPHome automatically installs PlatformIO as a dependency. You do **NOT** need to install PlatformIO separately.

---

### 🐧 Linux (Ubuntu/Debian-based)

Open a terminal:

    # Step 1: Install Python and pip
    sudo apt update
    sudo apt install -y python3 python3-pip

    # Step 2: Upgrade pip
    python3 -m pip install --upgrade pip

    # Step 3: Install ESPHome
    pip3 install --user esphome --upgrade

    # Step 4: Verify installation
    esphome version

> 💡 **Note:** If `esphome` is not found after installation, add `~/.local/bin` to your `PATH`:
>
>     echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
>     source ~/.bashrc

---

## ⚙️ ESPHome Commands Reference

Below are essential commands for managing your ESPHome project:

    # Show installed ESPHome version
    esphome version

    # Clean previous build files (recommended before first compile)
    esphome clean Marstek-Lilygo-ESPHome-Config.yaml

    # Compile only (check for errors without flashing)
    esphome compile Marstek-Lilygo-ESPHome-Config.yaml

    # Compile and flash via USB
    esphome run Marstek-Lilygo-ESPHome-Config.yaml

    # Open ESPHome Dashboard (web interface)
    esphome dashboard

> 💡 **Note:** The first flash **must** be done via USB. Subsequent updates can be done over-the-air (OTA).

---

## 🔌 First-Time USB Flashing

### Step 1: Connect the Board

1. Connect the LilyGO T-CAN485 to your PC using a USB-C cable
2. **If Windows**: Wait for driver installation to complete
3. **If the board is not detected**:
   - Unplug the board
   - Hold the **BOOT** button
   - While holding BOOT, plug in the USB cable
   - Release BOOT after 2 seconds
   - The board should now appear as a COM port

### Step 2: Flash the Firmware

    esphome run Marstek-Lilygo-ESPHome-Config.yaml

**What happens:**
1. ESPHome compiles the firmware
2. Automatically detects the COM port (or prompts you to select)
3. Uploads the firmware to the board
4. Shows live serial logs

### Step 3: Manual Port Selection (if needed)

If auto-detection fails, specify the port manually:

    # Windows example:
    esphome run Marstek-Lilygo-ESPHome-Config.yaml --device COM3

    # Linux example:
    esphome run Marstek-Lilygo-ESPHome-Config.yaml --device /dev/ttyUSB0

**Find your COM port:**
- **Windows**: Device Manager → Ports (COM & LPT)
- **Linux**: Run `ls /dev/ttyUSB*` or `ls /dev/ttyACM*`

---

## 📡 After First Flash

Once the initial USB flash succeeds:

✅ **The device will:**
- Connect to Wi-Fi (configured in your `secrets.yaml`)
- Become available for **OTA updates** (no USB needed)
- Show up in Home Assistant (if ESPHome integration is installed)

### ESPHome Dashboard

Start the ESPHome Dashboard to manage your devices via web browser:

    esphome dashboard

- Opens at: [**http://localhost:6052**](http://localhost:6052)
- Shows all your ESPHome devices
- Upload updates via web interface
- View logs in real-time

### OTA Updates (Over-The-Air)

After the first USB flash, update wirelessly:

    esphome run Marstek-Lilygo-ESPHome-Config.yaml --device marstek.local

Or use the IP address:

    esphome run Marstek-Lilygo-ESPHome-Config.yaml --device 192.168.1.xxx

---

## 🧪 Troubleshooting

### Windows Issues

**❌ Device not detected / No COM port?**
- Install CH9102 or CH341 driver (see section above)
- Check Device Manager for "Unknown Device" with yellow warning
- Try a different USB cable (must support data, not charge-only)
- Try different USB ports (use USB 2.0 ports if USB 3.0 fails)
- Hold BOOT button while connecting

**❌ "Access Denied" or "Permission Error"?**
- Close any serial monitor tools (Arduino IDE, PuTTY, etc.)
- Disable antivirus temporarily
- Run PowerShell/CMD as Administrator

**❌ COM port appears but flashing fails?**
- Unplug the board
- Hold BOOT button + plug in + release after 2 seconds
- Try again with manual port: `--device COMx`

---

### Linux Issues

**❌ Permission denied: /dev/ttyUSB0**

Add your user to the `dialout` group:

    sudo usermod -aG dialout $USER

**Important:** Log out and log back in (or reboot) for changes to take effect.

**❌ Device not found?**

Check which port the board is using:

    ls /dev/ttyUSB*
    ls /dev/ttyACM*

Then specify manually:

    esphome run Marstek-Lilygo-ESPHome-Config.yaml --device /dev/ttyUSB0

---

### General Issues

**❌ Compilation fails?**
- Run `esphome clean Marstek-Lilygo-ESPHome-Config.yaml` first
- Check your internet connection (ESPHome downloads dependencies)
- Verify Python version: `python --version` (requires 3.8 or higher)
- Update ESPHome: `pip3 install esphome --upgrade`

**❌ "ModuleNotFoundError: No module named 'esphome'"?**
- Reinstall ESPHome: `pip3 install esphome --upgrade`
- Check if pip installed to correct Python: `python -m pip show esphome`

**❌ OTA upload failing?**
- Verify the device is powered on and connected to Wi-Fi
- Check device IP address in your router
- Ping the device: `ping marstek.local` or `ping 192.168.1.xxx`
- Check `secrets.yaml` for correct WiFi credentials
- Reflash via USB if OTA is completely broken

**❌ ESPHome Dashboard not opening?**
- Check if port 6052 is already in use
- Try: `esphome dashboard --open-ui`
- Access manually: Open browser and go to `http://localhost:6052`

---

## 📚 Useful Resources

- 🔗 [ESPHome Official Documentation](https://esphome.io)
- 🔗 [ESPHome Getting Started Guide](https://esphome.io/guides/getting_started_command_line/)
- 🔗 [LilyGO T-CAN485 GitHub Repository](https://github.com/Xinyuan-LilyGO/T-CAN485)
- 🔗 [CH9102 USB Driver Download](https://www.wch.cn/downloads/CH343SER_EXE.html)
- 🔗 [Home Assistant ESPHome Integration](https://www.home-assistant.io/integrations/esphome/)

---

## 📄 License

MIT – Free to use, share, and modify.
