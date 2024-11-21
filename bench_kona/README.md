## Bench Kona Logs

These logs are from the [Bench Kona](https://www.projectgus.com/2024/01/kona-motor-turns/) setup as it has evolved over time. Messages in these logs may be spoofed and therefore incorrect, or show the original modules in various failure states.

### 2023-11-20

These are the first logs where the motor started turning, and includes two logs of the motor running away to high rpm.

* 2023-11-19T11:57:59.166526-bench_kona.csv - Going to Drive, no motor turning.
* 2023-11-20T12:13:23.925122-bench_kona.csv - Going to Drive, motor runaway.
* 2023-11-20T12:16:04.729380-bench_kona.csv - Going to Drive, motor revs but no runaway.
* 2023-11-20T18:01:53.736442-bench_kona.csv - Going to Drive, motor runaway!
* 2023-11-20T18:21:46.413557-bench_kona.csv - Going to Park only

### 2024-11-21

(Huh, one year and one day after runaway...)

By this point the control system is much more mature, and the park actuator is
permanently locked so the motor can apply torque but it can't spin.

Logs 3 through 9 in this series rely on splitting the PCAN bus at junction
connector JE11. This is found hanging off the the loom on the front of the motor
stack below the HV junction box and has six CAN pairs joined in it:

1. EPCU (VCU connector on EPCU pins 9 and 10), includes a terminating resistor.
2. EWP (ER)
3. EWP (BMS)
4. OBC
5. EPCU (MCU connector on EPCU pins 28 and 29)
6. Outward facing connection to the rest of the vehicle

By moving one CAN link at a time to bus 1 and bridging them together it was
possible to log the modules on the motor stack in isolation.

#### 241121-1-normal-startup.log

Normal bench Kona startup.

#### 241121-2-drive-reverse-torque-nospin.log

Log starts with bench kona in Ready.

1. Put into Drive, brake off, press accelerator gently a few times, brake back on, wait for the inverter/motor noise to stop (maybe ~500ms later), then back to P (I think)
2. Put into Reverse, repeat same steps.
3. Then went to R by mistake and immediately to D
4. Then put brake off in D, applied some accelerator, then immediately put into N while noise still audible. Noise stopped almost immediately

#### 241121-3-bridged-can1-is-mcu.csv

The "EPCU MCU" connection was split out to CAN1 and bridged back into the main bus.

This log starts with a comms problem, needed to jiggle some of the temporary connections for the bridge and cycle the power.

Then working well, went to Ready and Drive, etc.

The bridged interface "bus 1" saw messages were ACKed while the Ignition was on, but did not receive *any* messages from the MCU CAN interface. This included UDS diagnostic messages even when reading DTCs from the "Motor Controller" UDS addresses. All of this communication appears to have gone via the other "VCU" CAN interface.

#### 241121-4-mcu-disconnected-ready-drive.csv

As for number 3, but the interface connected to the MCU connection was totally disabled.

Vehicle still ran, went to Ready and Drive, etc.

The only observed issue was fault code U0146 reported as coming from the VCU (not the MCU). Weirdly this is a apparently gateway communications fault code.

#### 241121-5-obc-on-can1.csv

Moved the OBC onto CAN bus 1 in the bridge. Normal operation, no faults showing.

#### 241121-6-ewp-bms-on-can1.csv

Moved the EWP (BMS) onto CAN bus 1 in the bridge. Accidentally started up with an IMMO fault due to disabling the immobiliser emulator, and with some wires crossed (had accidentally split CANH and CANL).

For this reason a few corrupt/invalid messages appear briefly on CAN1 near the
start of the log. These are errors, messages that were meant to be received only
by CAN0, and can be ignored.

#### 241121-7-ewp-er-on-can1.csv

Similar to 6 but connected EWP (ER) to CAN bus 1. Forgot to start the bridge, so
the log starts after EV Ready and with fault code U1116 reported in the VCU (I
think?) due to missing EWP messages.

Fault cleared and didn't re-occur after the bridge had been started.

#### 241121-8-epcu-vcu-on-can1.csv

Moving the main EPCU (VCU) connection to CAN bus 1. Everything worked as expected.

#### 241121-9-epcu-vcu-disconnected-no-ready.csv

Disconnecting the main EPCU (VCU) CAN connection entirely. Won't go into Ready, pumps spin up in failsafe mode (and then turn off due to run dry protection), no response from the EPCU or MCU over UDS diagnostic interfaces, etc.

Bunch of comms related DTCs logged in OBC, BMS, VCU, etc.

This was more of an experiment to see if the EPCU would fail over to using the MCU CAN bus link as a redundant interface. Doesn't seem like it...

