# Burn test, 18 October 2026, Kelowna BC

One Wildfire Alarm node, **biobot-002 ("HSD_2")**, recorded two ignitions,
each followed by a period without ignition. Times below are Kelowna local time
(PDT, UTC−7).

| File | Period | Local time | UTC | Samples |
|---|---|---|---|---|
| `ignition-1.json` | First ignition | 16:00–16:15 | 23:00–23:15 | 29 |
| `no-ignition-1.json` | First no-ignition period | 16:25–16:45 | 23:25–23:45 | 38 |
| `ignition-2.json` | Second ignition | 18:25–18:40 | 01:25–01:40 (19 Oct) | 28 |
| `no-ignition-2.json` | Second no-ignition period | 18:40–19:00 | 01:40–02:00 (19 Oct) | 35 |
| `node-alerts.json` | Alerts the node raised itself that evening | | | |

Each window includes samples with `start ≤ timestamp < end`. The node stopped
reporting at 18:57:50 local, so the last window ends there.

## Summary

| Period | PM2.5 median / max (µg/m³) | Gas resistance median / min (kΩ) | Node severity |
|---|---|---|---|
| First ignition | 11 / 96 | 50.8 / 21.3 | `alert` in 6 samples, `watch` in 1 |
| First no-ignition | 0 / 1 | 76.1 / 72.5 | `none` throughout |
| Second ignition | 6.5 / 62 | 57.0 / 48.0 | `watch` in 2 samples |
| Second no-ignition | 1 / 13 | 80.8 / 54.3 | `watch` in 2 samples |

The node raised an **alert** on its own at 16:04:08 local (PM2.5 88 µg/m³
against a learned baseline of 5.2) and cleared it at 16:07:37. See
`node-alerts.json`.

## Fields

Each window file has `window` (start and end in local time and UTC), `device`,
`units`, and `samples`, one per sensor reading, about every 30 seconds:

| Field | Unit | Sensor |
|---|---|---|
| `timestamp` | UTC, ISO 8601 | node clock |
| `timestampLocal` | PDT, ISO 8601 | same instant |
| `pm1`, `pm25`, `pm10` | µg/m³ | Bosch BMV080 |
| `obstructed` | true/false | BMV080 optical path blocked |
| `gasResistance` | kΩ (falls with smoke and VOCs) | Bosch BME688 |
| `temperature` | °C | BME688 |
| `humidity` | % RH | BME688 |
| `pressure` | hPa | BME688 |
| `altitude` | m, derived from pressure | BME688 |
| `anomalySeverity` | `none`, `watch`, `alert`, `critical`, `fault` | on-device detector |
| `anomalyScore` | integer | on-device detector |

`anomalySeverity` and `anomalyScore` are the node's assessment for the
10-minute batch the sample arrived in, not for that sample alone.

## Notes on the export

- **Duplicates removed.** In the raw database, some samples appear two or
  three times inside the same upload with identical values (2,394 rows
  in total, 1,224 of them repeats). Each file keeps one row per timestamp. No
  repeated sample had differing values.
- **Fault later that evening.** At 17:12 local the node reported its
  particulate sensor as failed (`pm_sensor_failed`). PM values were reported
  again in the second ignition window, but treat PM readings after 17:12 with
  that in mind.
- Exported from the Wildfire Alarm production database on 26 October 2026.
