# Kona EV CAN logs

Some CAN logs taken from various CAN buses of Hyundai Kona EV vehicles. To assist with reverse engineering and repurposing for EV conversions and other uses.

Log files are in the SavvyCAN "GVRET" CSV format.

## See also

* A DBC file derived mostly from these logs can be found at https://github.com/projectgus/kona-ev-dbc
* `CAN IDS.md` has some older preliminary notes about CAN IDs visible in these logs. Mostly superceded by the DBC repo.
* [This Google Sheet](https://docs.google.com/spreadsheets/d/1-9jZafV9eZeBUnPQo7qQHbX2-_4qZfWfRVpidoF1owA/edit#gid=660740603) from 2018 (author unknown) is mostly about Diagnostic OBD2 PIDs, but includes a useful diagram of the vehicle CAN bus architecture showing all the buses connected to the CAN gateway ("CGW" in the diagram, Hyundai seems to more typically call it the Integrated Gateway Power Module "IGPM").

## Vehicles

This repo contains logs from:

* 2019 Kona EV Highlander (crashed). Has VESS & Active Air Flap System missing from P-CAN (connector broken).
* 2021 Kona EV Highlander (crashed).

## Logs

Numbers `TS nnn` are log timestamps in seconds, correlated from videos or notes. Approximate only.

Many logs have accompanying videos that I haven't put in the repo (too big). If you're unsure about what happens at a particular time then you can open an Issue and ask me to check the video, I'll see what I can do.

Many of these logs become intermittent part way through due to consistently dropped messages, some kind of capturing issue. [More details in this blog post](https://www.projectgus.com/2023/10/kona-can-decoding/#bad-logs). There are notes below about if/when this seems to happen in each log.

### 221207-1-2019-pcan-dcan-vehicle-ready-scancodes-custompids.csv
Model: 2019

(Log has no dropped messages.)

Channel 0: PCAN, Channel 1: DCAN with an OBD2 BT dongle attached.

Vehicle is Powered On and Ready for the entire log.

First running a DTC scan in the [Torque Pro](https://play.google.com/store/apps/details?id=org.prowl.torque&gl=US) Android app for fault codes (none found by Torque, seems it doesn't support the Kona's diagnostics interface).

Starting around TS 337, running the [custom PIDs for Kona](https://github.com/JejuSoul/OBD-PIDs-for-HKMC-EVs/tree/master/Hyundai%20Kona%20EV%20&%20Kia%20Niro%20EV/extendedpids) monitoring values and scrolling through the available values (working).

### 221207-2-2019-pcan-dcan-vehicle-on-off-change-gears.csv
Model: 2019

(Log probably has no dropped messages.)

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

### 221207-3-2021-pcan-dc-fast-charge-fail.csv
Model: 2021

(Log may become intermittent after approx TS 695)

Channel 0: PCAN

Connecting to a DC fast charger while a charging system fault was present (charge
plug socket control connector had been unplugged in the engine bay.)

Charging session doesn't initiate, vehicle doesn't even show the charge
connector plug-in light on the cluster display.

TS 675 (approx) is when vehicle was connected to fast charger

### 221210-1-2021-dcan-car-scanner-elm327.csv
Model: 2021

(Log probably has no dropped messages.)

Channel 1: DCAN

Running the [Car Scanner ELM OBD2](https://www.carscanner.info/) app to scan for DTCs, with presets for Kona that ship in that app.

### 221210-2-2021-dcan-car-scanner-elm327-vcu-mcu-only.csv
Model: 2021

(Log probably has no dropped messages.)

Channel 1: DCAN

As above, but with only VCU & MCU selected in the app interface.

### 221210-3-2021-pcan-dcan-in-ready.csv
Model: 2021

(This log appears to become intermittent after TS320.)

Channel 0: PCAN, Channel 1: DCAN

Vehicle sitting in "Ready" but not driving. No fault codes present.

Diagnostic app on Channel 1 is running, session is alive but I think idle the whole time.

### 221210-4-2021-pcan-dcan-read-dtcs-with-app.csv

(TS 1115 the log becomes intermittent.)

### 221210-5-2021-pcan-dcan-car-scanner-read-all-params.csv

(TS 214 the log becomes intermittent.)

### 221211-1-2021-pcan-dcan-dc-fast-charge.csv
Model: 2021

(TS 325 log is probably intermittent.)

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

### 221217-1-2021-pcan-bcan-no-key.csv
Model: 2021

(This log probably has no dropped messages.)

Channel 0: PCAN, Channel 1: BCAN

Trying to start without smart key in vehicle.

Starts with vehicle "off" but not asleep.

0:00 == TS 303

* TS 316 Press "Start Stop" button, display says "Key not detected"s
* TS 321 Press "Start Stop button again", display says "Press POWER button with key".
* TS 325 Press one more time

### 221217-2-2021-pcan-bcan-drive-modes.csv
Model: 2021

(TS557 log probably becomes intermittent.)

Channel 0: PCAN, Channel 1: BCAN

Driving at low speeds on private property (gravel/dirt).

0:00 == TS 362.4
11:00 == TS 1020 (2s deviation?)

* TS 366 Foot on brake
* TS 371 Press "Start Stop" button, powering on
* TS 365 "Ready" light on dash
* TS 382 Foot off brake
* TS 388 Close left front door (two other signals on dash: bonnet is "open" and car will also complain about right door open for entire drive, chassis is bent).
* TS 402 Press "Drive" button, foot is off brake so stays in Park
* TS 412 Foot on brake, Press "Drive", change to Drive
* TS 417 Start moving (probably @ 416361000)
* TS 427 Stop moving briefly, still in Drive
* TS 436 Stop again
* TS 438 Switch to Park
* TS 442 Right front door open
* TS 445 Right font door closed
* TS 447 Bonnet closed
* TS 450 Right front door open
* TS 452 Right front door closed
* TS 456 Switch to Drive again
* TS 459 Moving again
* TS 473 Speed approx 20km/h
* TS 480 Start "hard" acceleration (ECO mode, not full accelerator pedal)
* TS 484 Reached approx 40km/h, slowed down again
* Switched to Park again somewhere around this
* TS 614 Press "Drive Mode", switched to "Normal"
* TS 617 Press "Drive Mode", switched to "Sport"
* TS 619 Press "Drive Mode", switched to "ECO"
* TS 620 Press "Drive Mode", switched to "Normal"
* TS 628 Press "Drive Mode" twice switched to "Sport" immediately to "ECO"
* TS 631 Press "Drive Mode", switched to "Normal"
* TS 641 Switch to Drive
* Driving at approx 20km/h
* Showing 384km range to empty, average 59.4kWh/100km (I think), 15C outside temp
* TS 727 Moving 20km/hr Showing 20kWh/100km on the instant meter
* TS 730 Stopped
* TS 731 Switch to Park
* TS 736 Front right door open
* TS 788 Switch to Drive, start moving
* TS 830 Accelerating, instantaneous meter pushes 60kWh/100km briefly
* TS 839 Accelerating, instantaneous meter pushes 60kWh/100km again
* TS 897 Stop, still in Drive
* TS 900 Start "Hard" acceleration (not full pedal)
* TS 910 Got to 50km/h, eased off
* TS 924 Stop, still in Drive
* TS 940 Press "Drive Mode", switch to "Sport"
* TS 941 Start moving again
* TS 991 Stop, still in Drive
* TS 993 Start "Hard" acceleration. Traction control definitely kicked in (gravel surface).
* TS 1002 Got to 50km/hr, easing off.
* TS 1022 Stop, still in Drive
* TS 1082 Start off again
* TS 1101 Instant meter down to 0kWhr/100km (downhill coasting)
* TS 1109 Press left regen paddle
* TS 1114 Press regen paddle several more times
* TS 1133 Switch to Reverse
* TS 1147 Switch to Drive
* TS 1153 Switch to Reverse
* TS 1159 Switch to Drive
* TS 1194 Regen paddle for max regen
* TS 1200 Regen reduce by one level
* TS 1209 More regen
* TS 1222 Releasing the regen (not sure what this means?)
* TS 1236 Adding more regen (level 3)
* TS 1255 Reduce regen to level 2
* TS 1257 Reduce regen to level 1
* TS 1258 Increase regen to level 2
* TS 1259 Increase regen to level 3
* TS 1262 Reduce regen in three clicks to level 0
* TS 1265 Increase regen in three clicks to level 3
* TS 1300 Moving 40ish km/h
* TS 1311 Switch to ECO mode, 385km range
* TS 1313 Switch to Normal mode, 380km range
* TS 1314 Switch to Sport mode, 375km range
* TS 1316 Switch to ECO mode

### 221217-3-2021-pcan-ccan-drive-modes-abs-traction.csv
Model: 2021

(TS 1574, log becomes intermittent)

Channel 0: PCAN, Channel 1: CCAN

Continuation of previous log, more driving at low speeds on private property (gravel/dirt). However, Channel 1 is now CCAN.

0:00 = TS 1469.2

* TS 1473 Start moving in Drive, ECO mode
* TS 1487 Coasting 20km/h
* TS 1492 Increase regen to level 3
* TS 1496 Regen to level 2
* TS 1499 Regen to level 1
* TS 1501 Regen to level 0
* TS 1575 Stopped (or almost stopped), still in Drive
* Resume moving a few seconds later
* TS 1643 Switch to Normal
* TS 1644 Switch to Sport
* TS 1648 Switch to ECO
* TS 1694 Stopped agin, still Drive
* TS 1702 "Hard" acceleration, trying to engage traction control
* TS 1706 "Hard" braking to a stop, engaging ABS
* TS 1710 Switch to Reverse
* TS 1717 Switch to Drive
* TS 1730 Showing about 18kWh/100km on the instantaneous readout

### 221217-4-2021-pcan-ccan-menus-no-video.csv
Model: 2021

(TS1890, log becomes intermittent)

Channel 0: PCAN, Channel 1: CCAN

Stationary vehicle. Messing about in the head unit Settings menu changing a bunch of settings (drive
modes, heads up display position, etc). However accidentally didn't video this
one, so no reference timestamps.

### 221217-5-2021-pcan-ccan-menus.csv
Model: 2021

(TS2298, log becomes intermittent)

Channel 0: PCAN, Channel 1: CCAN

Redoing the previous log, but with a reference video this time. Car is
stationary, messing around in the Settings menu of head unit.

Note: The head unit is connected to MCAN and PCAN only, so the CCAN channel may not
contain anything relevant (unknown).

0:00 = 2199

* TS 2205 Live "Energy consumption view". Shows:
  - Driving 0kW, 49% cumulative (ignition on - now)
  - Climate 0.00kw, 39%
  - Electronics 0.22kW, 12%
  - Battery care 0.00kW, ---%.
* TS 2207 "Electronics" changed 0.22kw To 0.23kW instantaneous usage
* TS 2211 "Back" to "Energy information" Settings display. Shows:
  - 88% Battery
  - "Range" 425km
  - "With climate off" 425km
  - DC charger 40 mins
  - AC (station) 1h55min
  - AC (portable) 4h30min
* TS 2217 Set "DC charger" charging limit to 50% (was 100%), "0 min"
* TS 2220 Set "DC charger" charging limit to 70%, "0 min"
* TS 2223 Set "DC charger" charging limit to 100%. Estimate to full shows 40 mins.
* TS 2228 Set "AC charger" charging limit to 50% (was 100%), "0 min"
* TS 2231 Set "AC charger" charging limit to 70%, "0 min"
* TS 2234 Set "AC charger" charging limit to 100%
* TS 2247 Press "Activate utility mode"
* TS 2256 "Utility mode on"
* "To deactivate utility mode press the start button". Press Start button.
* TS 2266 Power back on
* TS 2293 Enable "Smart recuperation" (automatic regen level)
* TS 2296 Disable "Smart recuperation"
* TS 2299 Enable "Smart recuperation"
* TS 2301 Disable "Smart recuperation"
* Enter "Display control" for Head-up display
* TS 2307 Set Head-up Display Display Height to 20 (was 12)
* TS 2311 Set Display Height to 1
* TS 2317 Set Display Height to 11
* TS 2317 Set Rotation to -5 (was 0)
* TS 2321 Set Rotation to +5
* TS 2328 Set Rotation to -5
* TS 2329 Set Rotation to 0
* TS 2333 Set Brightness to 20 (was 10)
* TS 2336 Set Brightness to 1
* TS 2339 Set Brightness to 10
* Enter "Content selection" for Head-up display
* TS 2344 Disable "Driving Convenience info"
* TS 2346 Enable "Driving Convenience info"
* TS 2348 Disable "Blind-spot safety info"
* TS 2349 Enable "Blind-spot safety info"
* TS 2352 Disable "Radio/Media info"
* TS 2354 Enable "Radio/Media info"
* Enter Vehicle Settings -> Cluster -> Service interval
* TS 2365 Reset service interval to "factory default"
* TS 2370 "Reset to factory settings" (confirmed done)
* Enter Vehicle Settings -> Cluster Energy Consumption
* TS 2375 Select "After recharging" (was "On vehicle start")
* TS 2378 Select "Manually"
* TS 2379 Select "On vehicle start"
* TS 2423 Reopen live "Energy consumption view". Shows:
  - Driving 0kW, ---%
  - Climate 0.00kW, ---%
  - Electronics 0.25kW, 100%
  - Battery care 0.00kW, ---%
  Note: Vehicle was restarted after going to "utility mode" before.
* TS 2425 Electronics 0.22kW
* TS 2426 "Heater on"
* TS 2430 "Heater off" ?

### 221217-6-2021-pcan-ccan-live-battery-monitor.csv
Model: 2021

(TS2556 log becomes intermittent)

Channel 0: PCAN, Channel 1: CCAN

Viewing the "Energy consumption view" screen while doing various things to
change the vehicle power consumption.

Note: The head unit is connected to MCAN and PCAN only, so the CCAN channel may not
contain anything relevant (unknown).

0:00 = 2449

* TS 2452 Nothing in use:
  - Driving 0kW, ---%
  - Climate 0.00kW, ---%
  - Electronics 0.21kW, 100%
  - Battery care 0.00kW, ---%
* TS 2459 Heat on, climate control to 27C, fan on max
* TS 2459 Climate to 0.06kW, Electronics to 0.32kW
* TS 2466 Climate to 1.57kW (rising steadily), Electronics to 0.37kW.
  - Cumulative Climate 1%, Electronics 99%
* TS 2469 Climate to 2.18kW (after steady rise), Electronics 0.37kW
  - Cumulative Climate 2%, Electronics 98%
* TS 2470 Heat Off, Climate to 0.00kW, Electronics 0.36kW
  - Cumulative Climate 3%, Electronics 97%
* TS 2472 AC on
* TS 2476 Climate control to 17C
* TS 2477 Climate to 0.10kW (coming up again), Electronics 0.38kW
  - Cumulative same as previous
* TS 2489 Climate to 0.35kW (after steady rise), Electronics 0.38kW
  - Cumulative Climate 4% Electronics 96%
* TS 2500 Climate to 0.23kW, Electronics 0.38kW
  - Cumulative Climate 5%, Electronics 95%
* TS 2510 Climate to 0.19kW, Electronics 0.35kW
  - Cumulative Climate 5%, Electronics 95%
* TS 2523 AC off. Turn on heated seats, heated steering wheel
* TS 2541 Electronics to 0.49kW
  - Cumulative Climate 6%, Electronics 94%
* TS 2552 Turn off heated seats, heated steering wheel
* TS 2556 Electronics to 0.22kW
  - Cumulative climate 6%, Electronics 94%
* TS 2576 Power off
* TS 2582 Power on again
* TS 2594 Switch to Drive
* TS 2603 15km/h, 2kW driving, 0.00 kW Climate, 0.21kW electronics
  - Cumulative Driving 3%, Climate ---%, Electronics 97%
* TS 2604 9kW Driving, 0.24kW Electronics
  - Cumulative Driving 7%, Electronics 93%
* TS 2605 -6kW Driving, 0.25kW Electronics
  - Cumulative Driving 8%, Electronics 92%
* TS 2606 -7kW Driving, 0.26kW Electronics
* TS 2609 -6kW Driving, 0.23kW Electronics
* TS 2610 1Kw Driving, 0.21kW Electronics
  - Cumulative Driving 1%, Electronics 99% (went down!)
* TS 2611 18kW Driving, 0.22kW Electronics
  - Cumulative Driving 2%, Electronics 98%
* (Lots of different driving numbers here)
* TS 2638 1kW Driving, 0.26kW Electronics
  - Cumulative Driving 15%, Electronics 85%
* TS 2647 Switch to Reverse
* TS 2665 Switch to Drive
* TS 2678 Switch to Reverse
* TS 2700 Switch to Park
* TS 2702 Power off

### 221228-1-2019-pcan-ccan-fuse-pulling-engine-bay.csv
Model: 2019

Channel 0: PCAN, Channel 1: CCAN

Purpose of this log is to help isolate IDs to modulates by power cycling the car
various times with different fuses pulled. Between each fuse pull there will be
a gap in the logs which the car is powered off (12V battery disconnected.)

This session was pulling fuses from the engine bay fuse box.

* TS 40 Power on "control" run with all fuses in
* TS 50 Disconnected

Remove EPCU fuses 1 & 2

* TS 226 Battery connected
* TS 240ish "Power on" (ACC mode)
* TS 260ish Power off
* TS 275 Battery disconnected

Remove OBC fuse (only, there are also adjacent fuses "Charger 1" & "Charger 2", see later log file.)

* TS 329 Battery connected
* TS 347 "Power on" (ACC mode)
* TS 363 Power off
* TS 372 Battery disconnected

Remove IEB (Brake unit) fuses 1 & 2

* TS 415 Battery connnected
* TS 430 "Power on" (ACC mode)
* TS 444 "Power off"
* TS 452 Battery disconnected

All fuses present (again)

* TS 475 Battery connected
* TS 490 Power on (ACC mode)


### 221228-2-2019-dcan-dtcs-session.csv
Model: 2019

Channel 0: DCAN

Pulling & clearing DTCs using Car Scanner app.

* First scan was for limited ECUs only. ABS showing non-active codes C1638(08), C16B6(87), C1806(87), C1614(87), C1643(08)
* Clear codes
* Second scan was for all ECUs:
  - "4WD" (???) U0110, U0293
  - "Onboard Charger (Hybrid/EV)" U0110, U0293
  - "Wireless power charger" B1603(00)
  - IGPM B1603(00), B2801(00), C167C(00), B2211(00), C1829(00)
  - Head Up Display B2801(00)
  - Smart Key Module B1603(00)
  - HVAC B1238(13)
  - Blind spot detection C1812(87), C1614(87)
  - ABS control unit C16B6(87), C1638(08)
  - Electric Power Steering C1812(87)
  - Lane assistance/Front view camera C1812(87), C1614(87), C16B6(87)
  - "Dashbaord" (cluster?) B1001(52)
* Clear codes

### 221228-3-2019-pcan-ccan-fuse-pulling-driver-and-engine-bay.csv
Model: 2019

Channel 0: PCAN, Channel 1: CCAN

Same as 221228-1-2019-pcan-ccan-fuse-pulling-engine-bay, but working from the driver's inside fuse panel (and then some from the engine bay).

Removing inside fuses from IGPM:

Disconnect smart key unit fuses (probably they are this. One is marked "PDM" in some references, plus all fuses marked with symbol like ↻ in fuse box. Fuses numbered 1,2,34 (each with the circular arrow symbol).

* Battery was reattached before logging started
* No power on - button causes head unit to light up, and "check steering wheel lock on dash"
* Press start button a couple more times during log
* TS 699 Battery disconnected

Disconnect FCA system

* TS 805ish Battery connected
* TS 825ish power on (ACC mode)
* TS 828ish power on to Ready
* TS 845 power off
* TS 859 Battery disconnected

Disconnect AC fuse

* TS 896 Battery connected
* TS 918 Power on (ACC moe)
* TS 925 Power on (Ready)
* TS 940 Power off
* TS 954 Battery disconnected

Removing engine bay fuses again from here:

Disconnect battery management fuse.

* TS 1037 Battery connected
* TS 1056 Power on ( ACC)
* TS 1083 Attempt power on to ready, fails. Turtle flashing on cluster. No visible battery level on cluster.
* TS 1099 Power off
* TS 1112 Battery disconnected

Disconnect OBC & Charger 1 & Charger 2 fuses (similar to previous log but removing all three).

* TS 1196 Battery connected
* TS 1208 Power on (ACC mode)
* TS 1215 Power on to Ready
* TS 1230 Power off
* TS 1241 Battery disconnected

All fuses present again:

* TS 1329 Battery connected again

### 221228-4-2019-ac-charging.csv

(TS2881 or maybe TS2935, log becomes intermittent.)

AC charging session with a portable AC charger, configured variously for 6A, 8A, 10A max current.

Charging starts around TS 26652.

### 221228-5-2019-scheduled-charge-session.csv

(TS3775, log becomes intermittent - may be later, as some "vehicle off time" in this log.)

Using same AC charger as previous session, set for 8A max current, with vehicle pre-configured to only allow charging during "off-peak" which starts at 12:40 (~6 minutes after logging starts).

### 230112-1-2019-pcan-bcan-ac-compressor-cycles.csv

(TS432, log becomes intermittent.)

### 230112-2-2019-pcan-ccan-drive-modes-incomplete.csv

(TS1226, log becomes intermittent.)

### 230112-3-2019-pcan-ccan-drive-modes-take2.csv

(TS1421.96, log becomes intermittent.)

### 230112-4-2019-pcan-ccan-driving.csv

(TS1014.5, log becomes intermittent.)

### 230112-5-2019-pcan-ccan-maybe-reconnect-front.csv

(Does not appear to become intermittent.)

### 230112-6-2019-pcan-ccan-no-key.csv

(Does not appear to become intermittent.)

### 230112-7-2019-pcan-ccan-power-off-on-ready-off.csv

(Does not appear to become intermittent.)

### 230112-8-2019-pcan-dcan-live-monitoring-params.csv

(TS2565 the log becomes intermittent.)

### 230606-1-2021-epcu-control-board-only.csv

(The log does not appear to become intermittent.)

This log is from a third vehicle, not the same as the other two. It's from only the EPCU control board removed from the EPCU unit and powered on the bench (no connections to gate driver board or resolver.)

Power supplied to the power input pins (12V to large pins 47 & 48, 12V to small pin 46 for ignition/on, GND to 16.)

CAN connections:

* P-CAN pins 9 & 10 (H & L). This P-CAN bus is active but didn't send any messages in this configuration.
* P-CAN pins 28 & 29 (H & L). This P-CAN bus is what was logged, as Channel 1.
* "CCP-CAN". Marked unused, does not seem to be powered on.

### 231016-2019-srscm-bench-pcan-ccan.csv
Model: 2019

Channel 0: PCAN, Channel 1: CCAN

This log is *only* the SRSCM (SRS Control Module, i.e. airbag ECU) powered on the bench with nothing connected except for the PCAN and CCAN buses and a 1K pullup resistor to 12V on the Crash Output signal.

The crash output signal showed 80% duty cycle 12V 50Hz PWM while this capture was taken.

### 240114-09-2021-creep-limit

Car is on a hill facing up. Testing limits of "creep" mode.

* Start with foot on brake in Park. Into Drive
* Take foot off brake, car doesn't move (hill too steep.)
* Put accelerator down, car moves forward
* Remove foot from accelerator (no brake)
* Car slows down, rolls backwards for a time, then comes to a stop (no brake applied)


### 240512-1-comp-can-pcan

2021 Kona.

Ch0 is "COMP CAN", the small CAN network between the A/C controller, compressor and PTC heater. Ch1 is PCAN.

Stationary vehicle, alternatively turning the A/C and the heater on and off.

### 240512-2-pcan-regen-brake

2021 Kona.

Ch0 PCAN. Using the regen braking "hold left paddle" feature (I think).

### 240512-3-pcan-rolling-neutral

2021 Kona.

Ch0 PCAN. Driving at low speed, switching to Neutral and rolling, then back to Drive while still rolling. Repeats a few times.

### 240512-4-pcan-paddle-braking

2021 Kona.

Ch0 PCAN. Using the regen braking "hold left paddle" feature to fully stop the vehicle.

### 240512-5-pcan-paddle-braking-2

2021 Kona.

Ch0 PCAN. Using the regen braking "hold left paddle" feature to fully stop the vehicle again.

### 240512-6-pcan-charge-door-open-closed

2021 Kona.

Ch0 PCAN. Opening and closing the charge port door (nothing connected to charge port.)
