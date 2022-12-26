# Kona EV CAN logs

Some CAN logs taken from various CAN buses of Hyundai Kona EV vehicles. To assist with reverse engineering and repurposing for EV conversions and other uses.

Log files are in the SavvyCAN "GVRET" CSV format.

## See also

* `CAN IDS.md` has some preliminary notes about CAN IDs visible in these logs.
* [This Google Sheet](https://docs.google.com/spreadsheets/d/1-9jZafV9eZeBUnPQo7qQHbX2-_4qZfWfRVpidoF1owA/edit#gid=660740603) from 2018 (author unknown) is mostly about Diagnostic OBD2 PIDs, but includes a useful diagram of the vehicle CAN bus architecture showing all the buses connected to the CAN gateway ("CGW" in the diagram, Hyundai seems to more typically call it the Integrated Gateway Power Module "IGPM").

## Vehicles

This repo contains logs from:

* 2019 Kona EV Highlander (crashed). Has VESS & Active Air Flap System missing from P-CAN (connector broken).
* 2021 Kona EV Highlander (crashed).

## Logs

Numbers 'TS nnn` are log timestamps in seconds, correlated from videos or notes. Approximate only.

### 221207-1-2019-pcan-dcan-vehicle-ready-scancodes-custompids.csv
Model: 2019
Channel 0: PCAN, Channel 1: DCAN with an OBD2 BT dongle attached.

Vehicle is Powered On and Ready for the entire log.

First running a DTC scan in the [Torque Pro](https://play.google.com/store/apps/details?id=org.prowl.torque&gl=US) Android app for fault codes (none found by Torque, seems it doesn't support the Kona's diagnostics interface).

Starting around TS 337, running the [custom PIDs for Kona](https://github.com/JejuSoul/OBD-PIDs-for-HKMC-EVs/tree/master/Hyundai%20Kona%20EV%20&%20Kia%20Niro%20EV/extendedpids) monitoring values and scrolling through the available values (working).

### 221207-2-2019-pcan-dcan-vehicle-on-off-change-gears.csv
Model: 2019
Channel 0: PCAN, Channel 1: DCAN

Car is stationary for the whole log, putting it through various modes.

Channel 1 has an OBD2 BT dongle attached, session is alive but no interactions.

* TS 574 Starts with vehicle Off (but not asleep, cluster is powered showing bonnet and driver's door open)
* TS 581 Press brake pedal down (stayed down rest of log)
* TS 584 Power on
* TS 585 Ready symbol on cluster, "System check" still showing on cluster, 207 km range, ECO mode
* TS 588 "System check done", "check active air flap system" warning on cluster.
* TS 593 Power off
* TS 600 Power on again, Ready and "System check" again
* TS 603 "System check done", "check active air flap system" on cluster again
* TS 614 Switched to Drive
* TS 619 Switched to Neutral
* TS 622 Switched to Reverse
* TS 624 Switched to Park
* TS 631 Engage park brake
* TS 634 Disengage park brake
* TS 637 Engage park brake
* TS 643 Power off
* TS 645 Release brake pedal

### 221207-3-2021-pcan-dc-fast-charge-fail.log
Model: 2021
Channel 0: PCAN

Connecting to a DC fast charger while a charging system fault was present (charge
plug socket control connector had been unplugged in the engine bay.)

Charging session doesn't initiate, vehicle doesn't even show the charge
connector plug-in light on the cluster display.

* 20221207-1644-2021-dc-fast-charge-fail.log
** 0 is PCAN
** 675 approx is when we connected to fast charger
** didn't take, likely due to the existing fault

### 221210-1-2021-dcan-car-scanner-elm327.log
Model: 2021
Channel 1: DCAN

Running the [Car Scanner ELM OBD2](https://www.carscanner.info/) app to scan for DTCs, with presets for Kona that ship in that app.

### 221210-2-2021-dcan-car-scanner-elm327-vcu-mcu-only.log
Model: 2021
Channel 1: DCAN

As above, but with only VCU & MCU selected in the app interface.

## 221210-3-2021-pcan-dcan-in-ready.log
Model: 2021
Channel 0: PCAN, Channel 1: DCAN

Vehicle sitting in "Ready" but not driving. No fault codes present.

Diagnostic app on Channel 1 is running, session is alive but I think idle the whole time.

### 221211-1-2021-pcan-dcan-dc-fast-charge.log
Model: 2021
Channel 0: DCAN, Channel 1: PCAN

Successful DC fast charge session.

Channel 1 is running Car Scanner app with live PID monitoring open.

* TS 129 Starts with car on, no warnings, in Ready, 400km range, 23C ambient temp, ECO mode
* TS 286 Power Off
* TS 326 "Charge Plug connected" light on cluster
* TS 339 (approx) "Begin Charge" via charging network app
* TS 382 charging session visible on cluster, "31 minutes (100%)"
* TS 384 "11kW" appears, number starts ramping up steadily
* TS 388 Number reaches "24kW", stays at this from now on
* TS 394 0.5kWhr charged (approx) according to charging network app
* TS 518 end charging session via charging network app
* TS 522 Charging status disappears from cluster
* TS 524 "Charge Plug connected" light out on cluster
