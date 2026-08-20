# RUIXU packages for YamBMS

This package set converts the supplied monolithic 12-battery RUIXU ESPHome
configuration into the package structure used by YamBMS 1.7.x.

## Files

- `bms_combine_RUIXU_RS485_modbus.yaml` configures the physical RUIXU RS485
  parser.
- `bms_sensors_RUIXU_RS485_bms_full.yaml` maps one RUIXU battery into the
  entity IDs required by YamBMS.
- `bms_combine_RUIXU_RS485_bms_full.yaml` connects one RUIXU battery directly
  to the main YamBMS combining node.
- `bms_modbus_RUIXU_RS485_bms_full.yaml` exposes one RUIXU battery from a
  remote YamBMS Modbus-server node.
- `YamBMS_RP_BMS_RUIXU_RS485_Modbus_server.yaml` is a complete 12-battery
  AtomS3 example that retains ESPHome API access.

## Important behavior

RUIXU uses Seplos framing with protocol version `0x21`. The supplied component
reports current and power ten times too high, so the sensor layer applies the
working `multiply: 0.1` correction from the source configuration.

The component does not expose charge-allowed, discharge-allowed, balancing, or
alarm states. The package therefore reports charging and discharging as allowed,
balancing as false, and the YamBMS error bitmask as zero. Physical BMS protection
continues to operate locally, but those protection states are not transmitted to
YamBMS until the external component exposes them.

## Installation

Copy the four package files into the `packages/bms` directory of the same
YamBMS repository or fork used by the main YAML. The package references to
`bms_base.yaml`, `bms_combine.yaml`, `bms_modbus_server.yaml`, and
`bms_temperature_sensor_6.yaml` resolve relative to that directory.

For a remote server, assign every `bms_id` a unique value across the entire
YamBMS RS485 network. The central client must import
`packages/bms/bms_combine_modbus_client.yaml` once for every matching ID.

