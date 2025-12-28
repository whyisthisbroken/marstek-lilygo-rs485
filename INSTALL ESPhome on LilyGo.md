# 🚀 ESPHome Installation & Flashing Guide  
### For LilyGO T-CAN485 & Marstek Battery Integration

This guide explains how to install ESPHome on **Windows or Linux** and flash it onto a **LilyGO T-CAN485** to communicate with **Marstek RS485 batteries** using a custom ESPHome configuration.

---

## 🛠️ Requirements

- Python 3.8 or higher (on Windows or Linux)
- USB-C cable
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

**After installation:**
1. Restart your PC
2. Connect the LilyGO board via USB-C
3. Check Device Manager → Ports (COM & LPT)
4. You should see a new COM port (e.g., COM3, COM5)

> 💡 **Linux users**: Drivers are usually included in the kernel. No installation needed.

---

## 💻 Installation

### ✅ Windows

Open a terminal (CMD or PowerShell):

    # Step 1: Upgrade pip
    python -m pip install --upgrade pip

    # Step 2: Install or update ESPHome
    pip3 install esphome --upgrade

> ⚠️ **Note**: PlatformIO is **NOT required** separately. ESPHome includes it automatically.

---

### 🐧 Linux (Ubuntu/Debian-based)

Open a terminal:

    # Step 1: Install Python and pip
    sudo apt update
    sudo apt install -y python3 python3-pip

    # Step 2: Upgrade pip
    python3 -m pip install --upgrade pip

    # Step 3: Install or update ESPHome
    pip3 install --user esphome --upgrade

> 💡 **Note:** If `esphome` is not found after installation, add `~/.local/bin` to your `PATH`.

---

## ⚙️ ESPHome Commands

Below are useful commands for managing and flashing your ESPHome project.

    # Show installed ESPHome version
    esphome version

    # Clean previous build files
    esphome clean Marstek-Lilygo-ESPHome-Config.yaml

    # Compile only (no flashing)
    esphome compile Marstek-Lilygo-ESPHome-Config.yaml

    # Compile and flash via USB
    esphome run Marstek-Lilygo-ESPHome-Config.yaml

> 💡 **Note:** The first flash must be done via USB. Subsequent uploads can be done over-the-air (OTA).

---

## 🔌 Flashing Tips

### USB Flashing (First-Time)

1. Connect the LilyGO T-CAN485 to your PC using a USB-C cable.
2. If the board is not detected:
   - Hold the **BOOT** button while plugging in the board.
   - Release BOOT once connected.
3. Run:

       esphome run Marstek-Lilygo-ESPHome-Config.yaml

### Specify Port Manually (if needed)

    # Example for Windows:
    esphome run Marstek-Lilygo-ESPHome-Config.yaml --device COM3

    # Example for Linux:
    esphome run Marstek-Lilygo-ESPHome-Config.yaml --device /dev/ttyUSB0

---

## 📡 After Flashing

Once the initial USB flash is successful:

- The device will connect to Wi-Fi (as configured in your YAML)
- It will be available for **OTA updates**
- You can now manage it from the **ESPHome Dashboard**:

      esphome dashboard

  This opens a web interface at [**http://localhost:6052**](http://localhost:6052) where you can manage all your ESPHome devices.

---

## 📚 Useful Resources

- 🔗 [ESPHome Documentation](https://esphome.io)
- 🔗 [LilyGO T-CAN485 GitHub Repository](https://github.com/Xinyuan-LilyGO/T-CAN485)
- 🔗 [CH9102 Driver Download](https://www.wch.cn/downloads/CH343SER_EXE.html)

---

## 🧪 Troubleshooting

### Windows Issues

**Device not detected?**
- Install CH9102/CH341 driver (see USB Driver section above)
- Check Device Manager for unknown devices
- Try a different USB cable (must support data transfer)
- Hold BOOT button while connecting

**COM port not appearing?**
- Reinstall USB driver
- Try different USB ports
- Disable antivirus temporarily during driver installation

### Linux Issues

**Permission error on Linux**  
Add your user to the `dialout` group and reconnect the device:

    sudo usermod -aG dialout $USER

Then **log out and log back in** for changes to take effect.

**Device not found?**  
Use `esphome run Marstek-Lilygo-ESPHome-Config.yaml --device /dev/ttyUSBx` to specify manually.

### General Issues

**Compilation fails?**
- Run `esphome clean Marstek-Lilygo-ESPHome-Config.yaml` first
- Check your internet connection (downloads dependencies)
- Verify Python version: `python --version` (3.8+ required)

**OTA upload failing?**  
- Reboot the device manually
- Verify Wi-Fi config in secrets.yaml
- Check device IP address
- Reflash via USB if necessary

---

## 📄 License

MIT – Free to use, share, and modify.
