# Marstek Venus E ESPHome Configuration

<img width="1280" height="640" alt="repo-social-preview" src="https://github.com/user-attachments/assets/5fe73238-faf5-418a-a4f7-bd77f6a0a57c" />

[![ESPHome](https://img.shields.io/badge/ESPHome-2026.9%2B-blue.svg)](https://esphome.io/)
[![Hardware](https://img.shields.io/badge/Hardware-LilyGO%20T--CAN485-green.svg)](https://www.lilygo.cc/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

ESPHome configuration for monitoring and controlling a Marstek Venus E battery over Modbus RTU (RS485) with a LilyGO T-CAN485 board. It provides battery, inverter and energy sensors, charging controls, diagnostics, a local web interface and Home Assistant integration.

## Features

- Battery, AC, cell-voltage and temperature measurements
- Runtime estimate based on battery power, state of charge and configured cutoff values
- Daily, monthly and lifetime charge/discharge readings
- Work-mode selection, charge/discharge controls and power limits
- 24 warning binary sensors
- ESPHome web interface, encrypted API and OTA updates
- Status LED and device diagnostics

## Hardware

| Component  | Requirement                                    |
| ---------- | ---------------------------------------------- |
| Controller | LilyGO T-CAN485 (ESP32)                        |
| Battery    | Marstek Venus E                                |
| Connection | RS485 using the battery's Weipu SP17 connector |

The battery's included cable can be used, or a suitable two-wire RS485 cable can be connected to A+/B-. Check the connector pinout before making a cable.

## Install

See [INSTALL ESPhome on LilyGo.md](INSTALL%20ESPhome%20on%20LilyGo.md) for USB driver, first-flash and troubleshooting instructions. The configuration targets ESPHome 2026.9 or newer.

Place `secrets.yaml` next to `Marstek Lilygo ESPHome Config.yaml`:

```yaml
wifi_ssid: "YourWiFiName"
wifi_password: "YourWiFiPassword"
api_encryption_key: "YourBase64Key"
```

Generate a 32-byte API key with:

```sh
openssl rand -base64 32
```

The OTA platform reuses the API encryption key. No separate OTA password is configured. Keep `secrets.yaml` private and do not commit it.

Compile and flash over USB:

```sh
esphome compile "Marstek Lilygo ESPHome Config.yaml"
esphome run "Marstek Lilygo ESPHome Config.yaml"
```

After the first USB flash, use the ESPHome dashboard or run `esphome run` with `--device marstek.local` or the device's current IP address for OTA updates.

The configuration uses DHCP by default. Static addressing and `use_address` are optional; uncomment and set them in the YAML only if your network requires it.

## Home Assistant

Add the device through the ESPHome integration. Home Assistant can discover it automatically; otherwise add the ESPHome integration manually and provide the device's address and API encryption key.

The repository includes [Home Assistant Template.yaml](Home%20Assistant%20Template.yaml) for monthly and lifetime efficiency sensors. Add the entries under `template:` in your Home Assistant configuration. The templates use the entity IDs from this setup; if your existing entity IDs differ, update the source IDs in the template to match your Home Assistant entity registry.

## Polling and Performance

### Polling intervals

The clearest response-time gains come from refreshing battery power and remaining capacity more often:

| Metric                     | Upstream configuration                                                  | This configuration                                                                         | Difference vs. upstream                                    |
| -------------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
| Battery power polling      | 5-second controller with `skip_updates: 2` (nominally every 15 seconds) | 3-second controller                                                                        | 5x more frequent polling; 80% shorter interval             |
| Remaining capacity refresh | Fixed 300-second interval                                               | Recalculated on state-of-charge updates (15 seconds) and total-energy updates (60 seconds) | Up to 20x more frequent source updates                     |
| Modbus scheduling          | One 5-second controller with per-sensor skipped updates                 | Four controller groups: 3, 15, 60 and 300 seconds                                          | Fast measurements and slow counters use separate schedules |

These are configured update intervals, not measured API or display latency.

### ESP-IDF build tuning

These build-time settings are separate from the Modbus polling intervals:

- **Compiler:** `compiler_optimization: PERF` selects `-O2` rather than ESPHome's default size optimization. It can improve execution speed, at the cost of a larger binary.
- **Flash:** 80 MHz QIO increases flash access bandwidth. The flash chip and board wiring must support both settings. The Winbond-specific driver reduces unused flash-driver code and IRAM use; it assumes the chip is Winbond.
- **IRAM:** `sram1_as_iram` makes up to 40 KB more IRAM available. The lwIP base and TCP-specific options place networking hot paths in IRAM; ESP-IDF 5.5.6 estimates about 10 KB and 17 KB respectively. Wi-Fi and UART IRAM options also trade IRAM for throughput or interrupt handling.
- **Wi-Fi buffering:** The T-CAN485 has no PSRAM. With AMPDU RX enabled, ESP-IDF 5.5.6 therefore defaults to 10 static RX buffers and an RX BA window of 6. This configuration raises the buffer count to 16, using about 9.6 KB more static RAM than that default. The 16 buffers are enough for the current BA window; no custom window size is set.
- **Resource reductions:** Bluetooth and IPv6 are disabled, and Newlib Nano formatting and reduced log levels reduce unused features or code. Nano formatting does not support all `printf` features; disabling Bluetooth or IPv6 removes those capabilities.
- **Hardware requirements:** The configuration requires ESP32 revision 3.1 or newer, 4 MB flash, a Winbond flash chip and QIO at 80 MHz. These are compatibility constraints, not universal optimizations; unsupported hardware may fail to boot.

Several explicit values repeat ESP-IDF 5.5.6 defaults: AMPDU RX/TX, Wi-Fi IRAM and RX IRAM optimization, `CONFIG_LWIP_SO_REUSE=y`, `CONFIG_LWIP_STATS=n`, and `CONFIG_LWIP_TCPIP_RECVMBOX_SIZE=32`. `enable_lwip_tcpip_core_locking: true` and `disable_libc_locks_in_iram: true` also match ESPHome's defaults. These values are explicit in the YAML but are not additional gains over the defaults. The effects described here are configuration intent, not measured percentage improvements; numeric performance claims require controlled runtime tests.

No CPU frequency is set explicitly. ESPHome defaults the original ESP32 to its maximum supported frequency of 240 MHz.

## Sensors and Controls

The configuration exposes battery voltage, current, power, state of charge, total energy, remaining capacity, cell voltage range, cell-temperature range, AC measurements and charge/discharge energy counters. It also exposes battery and ESP Wi-Fi information, software and firmware versions, inverter state, warning sensors, work modes, backup settings, SOC targets and power limits.

Sensor entity IDs in Home Assistant can be retained from an earlier registration even when an ESPHome entity name changes. The efficiency templates therefore use explicit source entity IDs; verify those IDs in Home Assistant before relying on the calculated values.

## Screenshots

### ESPHome Web Interface

![Cell information, AC/DC measurements and energy counters](https://github.com/user-attachments/assets/6f2a7cac-d7bf-4e45-9db6-323d5c5d9d01)

![Network status and controls](https://github.com/user-attachments/assets/b6b21a96-07e5-4f58-a18d-c9c09eeb5633)

![Diagnostics and warning sensors](https://github.com/user-attachments/assets/83ee8ad7-f7c0-404c-b1ed-75f94fa9adee)

## Credits and References

- [Original Marstek Venus LilyGO RS485 project by Superduper1969](https://github.com/Superduper1969/MarstekVenus-LilygoRS485)
- [ha-marstek-venus integration by scruysberghs](https://github.com/scruysberghs/ha-marstek-venus)
- [Marstek Venus C/E Modbus register documentation](https://duravolt.nl/wp-content/uploads/Duravolt-Plug-in-Battery-Modbus.pdf)
- [LilyGO T-CAN485 documentation](https://github.com/Xinyuan-LilyGO/T-CAN485)
- [Modbus register spreadsheet](https://docs.google.com/spreadsheets/u/0/d/e/2PACX-1vSyu0LKoSrQQzvrosMH-sOcSKT7pgHSXEwAcIJe0cy3NCrmwiLH6VDGjh0_2HOKhL0nZmnI3Mk5Fb_d/pubhtml?gid=319238506&single=true&pli=1)
- [ESPHome documentation](https://esphome.io/)
- [Home Assistant ESPHome integration](https://www.home-assistant.io/integrations/esphome/)

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE).
