# SOFAR Modbus ESPHome Builder

Configuration files for reading SOFAR inverter data through Modbus using an ESP board and publishing the values directly to Home Assistant through ESPHome.

This project was tested with a **SOFAR HYD-3600-EP** inverter. It may also work with other SOFAR models, but some Modbus register addresses may need to be changed depending on the inverter model and firmware version.

![SOFAR Home Assistant Dashboard](docs/images/dashboard-preview.jpg)

## What this project includes

- ESPHome YAML configuration for SOFAR Modbus communication.
- Home Assistant dashboard YAML for monitoring the inverter.
- PV1 and PV2 voltage, current, and power readings.
- Battery voltage, current, power, SOC, SOH, temperature, and cycle readings.
- Grid, PCC, load, inverter output, frequency, and system status readings.
- Optional BMS values when supported by the inverter and battery system.
- A dashboard layout for live power summary, power flow, PV production, battery details, and system overview.

## Repository files

| File | Description |
|---|---|
| `solar-esp32.yaml` | ESPHome configuration for reading SOFAR inverter registers through Modbus. |
| `dashboard.yml` | Home Assistant dashboard YAML using the ESPHome sensor entities. |
| `docs/images/dashboard-preview.jpg` | Dashboard preview image used in this README. |

## Hardware used

- ESP board, such as ESP32, ESP32-S3, ESP8266, or NodeMCU depending on your YAML configuration.
- TTL to RS485 / MAX485 module.
- SOFAR inverter with Modbus RS485 support.
- Home Assistant with ESPHome installed.

## Wiring example

The included configuration uses an RS485 adapter connected to the ESP board UART pins.

Example wiring used in the YAML:

```text
ESP / NodeMCU GPIO13  -> RS485 DI
ESP / NodeMCU GPIO12  <- RS485 RO
ESP / NodeMCU GPIO14  -> RS485 DE + RE
ESP / NodeMCU GND     -> RS485 GND
ESP 3.3V or VIN       -> RS485 VCC, depending on your RS485 module

RS485 A               -> SOFAR RS485 A / Pin 5
RS485 B               -> SOFAR RS485 B / Pin 6
```

> Important: RS485 A/B labeling is not always consistent between modules and inverter manuals. If communication does not work, try swapping A and B.

## ESPHome configuration

The main ESPHome file is:

```text
solar-esp32.yaml
```

It defines:

- UART communication at `9600` baud.
- Modbus controller address `0x01`.
- Update interval of `10s`.
- Modbus register sensors for system state, grid, load, PV, battery, and BMS values.
- A text sensor that converts SOFAR system state codes into readable states such as `Wait`, `Check`, `On Grid`, `EPS`, and `Error`.

## Home Assistant dashboard

The dashboard file is:

```text
dashboard.yml
```

It provides a Home Assistant dashboard for visual monitoring of:

- System overview.
- Live power summary.
- Live power flow.
- PV production history.
- Battery SOC and power.
- PV details.
- Battery details.
- Grid and load values.

To use it:

1. Open Home Assistant.
2. Go to **Settings > Dashboards**.
3. Create or open a dashboard.
4. Use YAML mode or copy the cards from `dashboard.yml` into your dashboard configuration.
5. Make sure the entity names match the entities created by ESPHome.

## Installation steps

1. Clone or download this repository.

```bash
git clone https://github.com/AlRiachi/Sofar-modbus-esphomebuilder.git
```

2. Copy `solar-esp32.yaml` into your ESPHome configuration folder.

3. Update the Wi-Fi secrets in Home Assistant:

```yaml
wifi_ssid: "YOUR_WIFI_NAME"
wifi_password: "YOUR_WIFI_PASSWORD"
```

4. Check the board type in the ESPHome YAML and change it if needed.

Example for ESP32:

```yaml
esp32:
  board: esp32dev
  framework:
    type: arduino
```

Example for ESP8266 / NodeMCU:

```yaml
esp8266:
  board: nodemcuv2
```

5. Confirm the UART pins match your wiring.

6. Flash the ESP device using ESPHome.

7. Add the ESPHome device to Home Assistant.

8. Import or copy the dashboard YAML.

## Modbus settings

Default values used by this project:

| Setting | Value |
|---|---|
| Baud rate | `9600` |
| Data bits | `8` |
| Parity | `NONE` |
| Stop bits | `1` |
| Modbus address | `0x01` |
| Update interval | `10s` |

If your inverter uses a different Modbus slave address, update this section in the YAML:

```yaml
modbus_controller:
  - id: sofar_inverter
    address: 0x01
```

## Notes for other SOFAR models

This configuration was prepared for **SOFAR HYD-3600-EP**. For other models, you may need to:

- Change Modbus register addresses.
- Change scaling filters such as `multiply: 0.1`, `multiply: 0.01`, or `multiply: 10`.
- Remove unsupported BMS registers.
- Rename entities to match your Home Assistant dashboard.
- Adjust UART pins according to your ESP board.

## Security note

Before publishing or sharing your YAML files, remove or replace any private values such as:

- ESPHome API encryption keys.
- OTA passwords.
- Wi-Fi credentials.
- Any secrets copied directly into YAML files.

Use ESPHome `secrets.yaml` wherever possible.

## Troubleshooting

### No data is received

- Check RS485 A/B wiring and try swapping them.
- Confirm the inverter Modbus address.
- Confirm baud rate and serial settings.
- Make sure the inverter RS485 port is enabled.
- Check ESPHome logs with `VERY_VERBOSE` Modbus logging.

### Values are wrong

- Confirm the register map for your exact inverter model.
- Check whether the register should be signed or unsigned.
- Review the multiplier filter used for that sensor.

### Dashboard entities show unavailable

- Make sure the ESPHome device is online.
- Confirm the entity IDs in Home Assistant match the dashboard YAML.
- Rename dashboard entities or update the dashboard YAML as needed.

## Disclaimer

This project is shared for personal and community use. Working with inverters and electrical systems can be dangerous. Make sure wiring is done safely and according to your inverter manual. Use this configuration at your own risk.

## Contributions

Feedback, fixes, and improvements are welcome. If you test this with another SOFAR model, please share the model name and any register changes needed.
