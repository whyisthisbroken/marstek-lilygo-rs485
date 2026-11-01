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

The Modbus controllers in this configuration use 3-, 15-, 60- and 300-second intervals, grouping registers by update frequency. The fast controller covers measurements such as battery power. Derived remaining-capacity and runtime values are recalculated when their source values change. The UART receive buffer is explicitly set to 512 bytes, and the ESP-IDF configuration enables selected Wi-Fi and compiler optimizations.

Compared with [`lilygo-rs485.yaml` on the upstream project's main branch](https://github.com/Superduper1969/MarstekVenus-LilygoRS485/blob/main/lilygo-rs485.yaml), the upstream configuration uses one 5-second Modbus controller and sets `skip_updates: 2` for battery power. That gives a nominal 15-second polling interval for that sensor, compared with 3 seconds here. This is a configuration-level interval comparison, not a measured end-to-end latency benchmark.

The upstream remaining-capacity template uses a 300-second update interval. This configuration instead updates the derived value from state-of-charge and total-energy readings. RAM use, firmware size, Wi-Fi throughput and total network or bus traffic have not been compared under controlled, identical build and runtime conditions, so no numerical claims are made for them here.

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
