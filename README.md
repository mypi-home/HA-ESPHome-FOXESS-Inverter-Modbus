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
┌─────────┐              ┌─────────────┐           ┌─────────────────┐
│         │              │           DI│<--------->│TX               │
│  FOX    │<-----B- ---->│  RS485    DE│<--\(join) │         ESP32/  │
│  ESS    │<---- A+ ---->│  to TTL   RE│<---+----->│GPIO0   ESP8266  │
│ H1/AC1  │              │  module   RO│<--------->│RX               │
│         │              │             │           │                 │
│         │              │          VCC│<--------->│3.3V          VCC│<--
│         │              │          GND│<--------->│GND           GND│<--
└─────────┘              └─────────────┘           └─────────────────┘
DE+RE wired together

```

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

```yaml
substitutions:
  name: solar-powermeter

esphome:
  name: ${name}
  platform: ESP32
  board: esp-wrover-kit

external_components:
  - source: github://syssi/esphome-modbus-solax-x1@main
    refresh: 0s

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

ota:
# api:

logger:
  baud_rate: 0

mqtt:
  broker: !secret mqtt_host
  username: !secret mqtt_username
  password: !secret mqtt_password
  id: mqtt_client

uart:
  baud_rate: 9600
  tx_pin: GPIO1
  rx_pin: GPIO3

solax_x1:
  serial_number: "3132333435363737363534333231"
  address: 0x0A
  update_interval: 1s
#  flow_control_pin: GPIO0

text_sensor:
  - platform: solax_x1
    mode_name:
      name: "${name} mode name"
    errors:
      name: "${name} errors"

sensor:
  - platform: solax_x1
    ac_power:
      name: "${name} ac power"
    energy_today:
      name: "${name} energy today"
    energy_total:
      name: "${name} energy total"
    dc1_voltage:
      name: "${name} dc1 voltage"
    dc2_voltage:
      name: "${name} dc2 voltage"
    dc1_current:
      name: "${name} dc1 current"
    dc2_current:
      name: "${name} dc2 current"
    ac_current:
      name: "${name} ac current"
    ac_voltage:
      name: "${name} ac voltage"
    ac_frequency:
      name: "${name} ac frequency"
    temperature:
      name: "${name} temperature"
    runtime_total:
      name: "${name} runtime total"
    mode:
      name: "${name} mode"
    error_bits:
      name: "${name} error bits"
```

For a more advanced setup take a look at the [esp32-example-advanced-multiple-uarts.yaml](esp32-example-advanced-multiple-uarts.yaml).

## Known issues

# TBA

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

# Add many more discovered Modbus addresses
# Update for alternative hardware LilyGO CAN485
# Add code to pull and paste data from BMS CAN Bus

## References
* https://github.com/nathanmarlor/HA-FoxESS-Modbus
* https://github.com/esphome/esphome/blob/dev/esphome/components/modbus/modbus.cpp
* https://github.com/syssi/esphome-modbus-solax-x1/ - approach and documentation
