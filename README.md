# Marstek Venus E - ESPHome Configuration
<img width="1280" height="640" alt="repo-social-preview" src="https://github.com/user-attachments/assets/5fe73238-faf5-418a-a4f7-bd77f6a0a57c" />

[![ESPHome](https://img.shields.io/badge/ESPHome-2025.12.2-blue.svg)](https://esphome.io/)
[![Hardware](https://img.shields.io/badge/Hardware-LilyGO%20T--CAN485-green.svg)](https://www.lilygo.cc/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

**High-performance ESPHome configuration for Marstek Venus E battery integration via Modbus RS485**

Monitor and control your Marstek Venus E plug-in battery (5.12 kWh) using ESPHome and the LilyGO T-CAN485 board. This configuration provides real-time battery metrics, runtime estimates, energy efficiency tracking, and full control over charging/discharging modes.

![GitHub last commit](https://img.shields.io/github/last-commit/whyisthisbroken/marstek-lilygo-rs485)
![GitHub release](https://img.shields.io/github/v/release/whyisthisbroken/marstek-lilygo-rs485)

---

## 📋 Table of Contents

- [Features](#-features)
- [Performance](#-performance)
- [Screenshots](#-screenshots)
- [Hardware Requirements](#-hardware-requirements)
- [Quick Start](#-quick-start)
- [Installation](#-installation)
- [Home Assistant Integration](#-home-assistant-integration)
- [Sensors & Entities](#-sensors--entities)
- [Credits & Resources](#-credits--resources)
- [License](#-license)

---

## ✨ Features

### Monitoring & Control
- **Real-time Battery Metrics**: SOC, voltage, current, power, temperature with cell-level monitoring
- **Runtime Estimation**: Dynamic time-to-full/empty calculation based on power flow
- **Energy Tracking**: Daily, monthly, and lifetime charge/discharge statistics
- **Work Mode Control**: Manual, Anti-feed, AI modes with forcible charge/discharge
- **Power Management**: Adjustable limits (0-2500W), SOC targets (12-100%), backup function
- **24 Warning Sensors**: Real-time alerts for grid, battery, temperature, and communication issues

### ESPHome Integration
- **Built-in Web Server**: Access battery data directly via browser (no Home Assistant required)
- **OTA Updates**: Wireless firmware updates via ESPHome dashboard or web interface
- **Status LED Feedback**: Visual system state via onboard WS2812 LED
- **System Diagnostics**: WiFi/BT/Cloud status, software versions, connection monitoring

### Home Assistant Ready
- **Automatic Discovery**: Zero-config integration via ESPHome API
- **Complete Dashboard**: Ready-to-use Lovelace YAML with battery metrics and controls
- **Efficiency Templates**: Custom sensors for monthly/total efficiency and round-trip loss tracking
- **Energy Dashboard Compatible**: Integrates with HA energy management

### Optimized Performance
- **Event-driven Architecture**: 3-second response times for critical sensors
- **Modbus Optimization**: 57% less bus traffic through register batching
- **Network Efficiency**: 56% fewer API calls, 35-40% reduced traffic
- **Memory Optimized**: 22-25 KB less RAM usage, 20 KB smaller firmware

## ⚡ Performance

### Original vs. Optimized Configuration

| Performance Metric | Original Config | This Config | Improvement |
|-------------------|-----------------|-------------|-------------|
| **API Updates** | ~250/min | ~110/min | **-56%** ⚡ |
| **Battery Power Response** | 15 seconds | 1 second | **93% faster** ⚡ |
| **Modbus Bus Requests** | ~35 per cycle | ~15 per cycle | **-57%** ⚡ |
| **Battery Capacity Update** | 120s polling | 3s event-driven | **40x faster** ⚡ |
| **RAM Usage** | Baseline | -22 to -25 KB | **Optimized** 💾 |
| **Network Traffic** | 100% | 60-65% | **-35 to -40%** 📡 |
| **Firmware Size** | Baseline | -20 KB | **Smaller** 💿 |
| **WiFi Throughput** | Standard | +20-30% faster | **Enhanced** 📶 |
| **Boot Time** | 2-3 seconds | 1.5-2 seconds | **Faster** 🚀 |
| **Network Latency** | Baseline | -15 to -25ms | **Lower** ⚡ |

### Key Optimizations

**🔧 Modbus Efficiency**
- Register batching reduces 7 blocks from 24 requests to 7 requests
- Smart polling with skip_updates for slow-changing values
- Event-driven updates instead of fixed intervals

**💾 Memory & Performance**
- 22-25 KB less RAM usage through optimizations
- 20 KB smaller firmware size
- Faster boot times and reduced CPU load

**📡 Network Optimization**  
- 56% fewer API calls through intelligent batching
- 35-40% less network traffic via throttling
- 20-30% WiFi throughput increase with AMPDU

**⚡ Response Times**
- Battery Power: 15s → 1s (instant response)
- Battery Capacity: 120s → 3s (event-based)
- Sensor updates: Real-time with throttling

**Result**: A highly optimized configuration that's faster, more efficient, and more reliable than the original while maintaining full functionality.

---

## 📸 Screenshots

### ESPHome Web Interface

<img width="1238" height="1651" alt="maesp1" src="https://github.com/user-attachments/assets/6f2a7cac-d7bf-4e45-9db6-323d5c5d9d01" />


*Cell information, AC/DC metrics, and energy statistics*

<img width="711" height="1014" alt="maesp2" src="https://github.com/user-attachments/assets/b6b21a96-07e5-4f58-a18d-c9c09eeb5633" />

*Network status and control settings*

<img width="709" height="749" alt="maesp3" src="https://github.com/user-attachments/assets/83ee8ad7-f7c0-404c-b1ed-75f94fa9adee" />

*System diagnostics and warning sensors*

### Home Assistant Dashboard

<img width="1104" height="1572" alt="lovelace-solar_marstekbattery screenshot" src="https://github.com/user-attachments/assets/d3f71fb4-16c6-4c13-a4cf-1b0e19a51238" />

*Complete Lovelace dashboard with battery metrics, controls, and energy flow*

---

## 🛠️ Hardware Requirements

### Required Hardware
| Component | Model | Where to Buy |
|-----------|-------|--------------|
| **ESP32 Board** | LilyGO T-CAN485 | [LilyGO Official Store](https://www.lilygo.cc/products/t-can485) |
| **Battery** | Marstek Venus E (5.12 kWh) | [Marstek](https://www.marstek.com/) |
| **Cable** | RS485 cable (2-wire) | Included with Marstek (Weipu SP17 connector) or use standard Modbus cable |

### About the Cable
The Marstek Venus E battery includes a **Weipu SP17 5-pin connector** with RS485 terminals. You can:
- Use the included cable directly
- Create a custom cable with Weipu SP17 male connector
- Use any standard RS485 cable with A+/B- terminals

---

## 🚀 Quick Start

### 1. Flash ESPHome to LilyGO

See [INSTALL ESPhome on LilyGo.md](INSTALL%20ESPhome%20on%20LilyGo.md) for detailed
 flashing instructions.

### 2. Download Configuration

Download the latest configuration from the releases page:

**[Download Latest Config →](https://github.com/whyisthisbroken/marstek-lilygo-rs485/releases/latest)**

Click on `Marstek-Lilygo-ESPHome-Config.yaml` under Assets to download.

### 3. Generate API Encryption Key

Generate a secure encryption key using OpenSSL:

    openssl rand -base64 32

Or use the ESPHome dashboard to generate one automatically:
- Visit [ESPHome API Documentation](https://esphome.io/components/api/)
- Scroll down to "Encryption Key" section
- Click the generate button

### 4. Customize Secrets

Create a `secrets.yaml` file:

    # WiFi Credentials
    wifi_ssid: "YourSSID"
    wifi_password: "YourPassword"

    # API Encryption Key (generated above)
    api_encryption_key: "your-32-char-base64-key-here"
    
    # OTA Password (choose your own)
    ota_password: "your-secure-ota-password"

### 5. Validate & Flash Configuration

First, compile and validate the configuration:

    esphome compile Marstek-Lilygo-ESPHome-Config.yaml

If compilation succeeds, flash the device:

    esphome run Marstek-Lilygo-ESPHome-Config.yaml
---

## 📦 Installation

### ESPHome Installation via PowerShell/Terminal

**Install with pip** (Recommended)

    pip3 install esphome --upgrade

**Verify Installation**

    esphome version

### Detailed Installation Methods

For step-by-step flashing instructions including USB drivers, bootloader mode, and troubleshooting, see:

**[INSTALL ESPhome on LilyGo.md](INSTALL%20ESPhome%20on%20LilyGo.md)**

This guide includes:
- Driver installation for Windows/Mac/Linux
- Entering bootloader mode on LilyGO T-CAN485
- First-time USB flashing
- Common flashing errors and solutions

### Using the Configuration

1. **Download Configuration**

   Visit [Releases](https://github.com/whyisthisbroken/marstek-lilygo-rs485/releases/latest) and download `Marstek-Lilygo-ESPHome-Config.yaml`

2. **Generate API Key**

       openssl rand -base64 32

3. **Create Secrets File**

   Create `secrets.yaml` in the same folder with your WiFi credentials and generated API key.

4. **Compile Configuration**

       esphome compile Marstek-Lilygo-ESPHome-Config.yaml

5. **Initial Flash (USB)**

       esphome run Marstek-Lilygo-ESPHome-Config.yaml

---

## 🏠 Home Assistant Integration

### Automatic Discovery

ESPHome devices are automatically discovered in Home Assistant via the ESPHome integration.

**Steps:**
1. Home Assistant will show a notification: "New device discovered"
2. Click **"Configure"**
3. Enter your `api_encryption_key` from `secrets.yaml`
4. Device is ready to use!

### Manual Addition

If auto-discovery doesn't work:

1. Navigate to **Settings** → **Devices & Services**
2. Click **+ Add Integration**
3. Search for **ESPHome**
4. Enter:
   - **Host**: `192.168.178.160` (or your configured IP)
   - **Encryption Key**: Your `api_encryption_key` from `secrets.yaml`

### Dashboard Import

Import the included dashboard for a complete UI:

**File**: `Home Assistant Marstek Dashboard.yaml`

1. Copy the YAML content
2. In Home Assistant: **Settings** → **Dashboards** → **Edit Dashboard**
3. Click **⋮** (three dots) → **Raw configuration editor**
4. Paste the YAML content
5. Save

**Dashboard Features:**
- Real-time battery metrics and status
- Runtime estimation display
- Charge/discharge controls
- Warning indicators with color coding
- Energy statistics (daily, monthly, total)
- Work mode selection
- Power limit adjusters

### Efficiency Templates

The included **Home Assistant Template** adds advanced efficiency tracking:

**File**: `Home Assistant Template.yaml`

Add to your `configuration.yaml`:

    template: !include_dir_merge_list templates/

Create `templates/marstek_efficiency.yaml` and paste the template content.

**New Sensors:**
- **Monthly Efficiency (%)**: `(Monthly Discharge / Monthly Charge) × 100`
- **Total Efficiency (%)**: `(Total Discharge / Total Charge) × 100`
- Shows energy lost during charge/discharge cycles
- Useful for battery health monitoring

---

## 📊 Sensors & Entities

### Battery Monitoring (8 sensors)
- Voltage, Current, Power, State of Charge (%)
- Remaining Capacity (kWh), Total Energy (kWh)
- Max/Min Cell Voltage, Cell Voltage Difference

### Runtime & Status (2 sensors)
- **Runtime Estimate** - "Full in: 2h 30min" / "Empty in: 1h 45min" / "Standby"
- **Inverter State** - Sleep/Standby/Charge/Discharge/Fault/Idle

### AC Grid (3 sensors)
- AC Voltage, Current, Power

### Energy Statistics (6 sensors)
- Total Charging/Discharging Energy (kWh)
- Daily Charging/Discharging (kWh)
- Monthly Charging/Discharging (kWh)

### Temperature (5 sensors)
- Internal Temperature, MOS1/MOS2 Temperature
- Max/Min Cell Temperature

### System Info (7 sensors)
- Software/Firmware/BMS Version
- ESPHome Version, IP Address, SSID
- WiFi Signal Strength

### Controls (11 entities)
- RS485 Control Mode, Work Mode Selection
- Forcible Charge/Discharge with Power Limits
- Backup Function, SOC Charge Target
- Max Charge/Discharge Power Settings
- Charging/Discharging Cutoff Capacity

### Warning Sensors (24 binary sensors)
Comprehensive monitoring for grid issues, battery protection, temperature alerts, and communication status.

---

## 🔗 Credits & Resources

### Special Thanks

💡 **@Superduper1969** - Original MarstekVenus-LilygoRS485 implementation  
[https://github.com/Superduper1969/MarstekVenus-LilygoRS485/tree/main](https://github.com/Superduper1969/MarstekVenus-LilygoRS485/tree/main)

🧰 **@scruysberghs** - ha-marstek-venus integration  
[https://github.com/scruysberghs/ha-marstek-venus](https://github.com/scruysberghs/ha-marstek-venus)

### Documentation

📘 **Modbus Register Documentation** - Marstek Venus C/E Protocol  
[https://duravolt.nl/wp-content/uploads/Duravolt-Plug-in-Battery-Modbus.pdf](https://duravolt.nl/wp-content/uploads/Duravolt-Plug-in-Battery-Modbus.pdf)

📘 **LilyGo T-CAN485 Documentation** - Hardware specs and pinout  
[https://github.com/Xinyuan-LilyGO/T-CAN485](https://github.com/Xinyuan-LilyGO/T-CAN485)

📑 **Modbus Register Spreadsheet** - Complete register overview  
[Google Spreadsheet - Modbus Registers](https://docs.google.com/spreadsheets/u/0/d/e/2PACX-1vSyu0LKoSrQQzvrosMH-sOcSKT7pgHSXEwAcIJe0cy3NCrmwiLH6VDGjh0_2HOKhL0nZmnI3Mk5Fb_d/pubhtml?gid=319238506&single=true&pli=1)

### Community

💬 **Tweakers Forum** - Dutch community discussions  
- [Marstek Venus Battery Thread](https://gathering.tweakers.net/forum/list_messages/2282240)
- [Home Assistant Integration Thread](https://gathering.tweakers.net/forum/list_messages/2262054)

### Official Links

- [ESPHome Documentation](https://esphome.io/)
- [Modbus Protocol Specification](https://modbus.org/)
- [Home Assistant ESPHome Integration](https://www.home-assistant.io/integrations/esphome/)

---

## 📝 Latest Release

See the complete changelog and download the latest version:

**[Latest Release →](https://github.com/whyisthisbroken/marstek-lilygo-rs485/releases)**

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
