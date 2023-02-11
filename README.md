# HA-ESPHome-FOXESS-Inverter-Modbus

Hardware setup guide and ESPHome/Home Assistant code to pull full data from FOX ESS H1/AC1 Hybrid Inverters and publish to Home Assistant


## Supported devices

* FOX ESS H1/AC1 - https://www.fox-ess.com/wp-content/uploads/2021/03/Hybrid-AC-User-Manual.pdf

## Requirements

* [ESPHome 1.18.0 or higher](https://github.com/esphome/esphome/releases).
* Ethernet cable wiring for connection to controller
* RS485-to-TTL module (`HW-0519` f.e.)
* Generic ESP32 or ESP8266 board

## Hardware Schematics

#### RS485-TTL module with flow control pin

```
                     RS485                        UART
┌──────────────┐              ┌─────────────┐           ┌─────────────────┐
│       |      │              │           DI│<--------->│TX               │
│  FOX  | PIN 3│<---- B ----->│  RS485    DE│<--\(join) │         ESP32/  │
│  ESS  | PIN 4│<---- A ----->│  to TTL   RE│<---+----->│GPIO0   ESP8266  │
│ H1/AC1|      │              │  module   RO│<--------->│RX               │
│       |      │              │             │           │                 │
│       |      │              │          VCC│<--------->│3.3V          VCC│<--
│       |      │              │          GND│<--------->│GND           GND│<--
└──────────────┘              └─────────────┘           └─────────────────┘
                                             DE+RE wired together

```
Pins are in the Meter/CT/RS485 Interface connector show in the manual on page 21.
 - PIN 3 = 485-B
 - PIN 4 = 485-A
Please make sure to power the RS485 module with 3.3V because it affects the TTL (transistor-transistor logic) voltage between RS485 module and ESP.

## Hardware Photos:

TBA

## Installation

Requires existing installation of Home Assistant and ESPHome

Use the `esp32-example.yaml` / `esp8266-example.yaml` as proof of concept:

```bash
# Setup new ESPHome Device with hardware setup above
# Paste configuration from src folder into the yaml for the new device
# Update substitute and Wifi values for your own
# Validate the configuration, create a binary, upload it, and start logs

```

## Configuration

See src/config.yaml

## Known issues

* TBA

## Debugging

If this component doesn't work out of the box for your device please update your configuration to enable the debug output of the UART component and increase the log level to the see outgoing and incoming serial traffic:

```
logger:
  baud_rate: 0
  level: DEBUG

uart:
  id: uart0
  baud_rate: 9600
  tx_pin: GPIO1
  rx_pin: GPIO3
  debug:
    direction: BOTH
```

## To-Do List

* Add many more discovered Modbus addresses
* Update for alternative hardware LilyGO CAN485
* Add code to pull and paste data from BMS CAN Bus


## References
* https://github.com/nathanmarlor/HA-FoxESS-Modbus
* https://github.com/esphome/esphome/blob/dev/esphome/components/modbus/modbus.cpp
* https://github.com/syssi/esphome-modbus-solax-x1/ - approach and documentation
