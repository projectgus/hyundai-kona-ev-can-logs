# CAN IDs

Starting to collect notes on CAN IDs seen in the logs.

## 0x109

| Bus  | Interval | Sender |
|------|----------|--------|
| PCAN | 10ms     | ???    |

Sample value: 00 01 00 FF FF FF 0F <counter>

Bytes:

* 0:
  - 0x00 normally?
  - 0x02 for about 10ms during Power On, about 300ms when switching Reverse->Park (indicates parking actuator moving?)
  - 0x04 for about 700ms when switching Park to Drive? (indicates park brake actuator moving?)
* 1: 01
* 2:
  - 0x00 when Off
  - 0xFC when "powering on" (or "check in progress")
  - 0xFD when "ready/check passed"
* 3: FF
* 4: FF
* 5: FF
* 6: 0F
* 7: counter, seems to change both high and low nibbles each time (two 4-bit counters?)

## 0x10C

| Bus  | Interval | Sender |
|------|----------|--------|
| PCAN | 10ms     | ???    |

Sample value: 04 00 54 15 40 01 24

Bytes:

* 0 & 1:
  - When in Park, 0x04 0x00 (Off or On)
  - Sequence when entering Drive:
    * 0x02 0x10 for ~200ms
    * 0x00 0x10 for ~400ms
    * 0x01 0x00 until switching back to Park
  - Sequence when switching back to Park:
    * 0x02 0x10 for ~300ms
    * 0x04 0x00 again

* 7: counter, changes both nibbles each time

### 0x153

| Bus  | Interval | Sender |
|------|----------|--------|
| PCAN | 10ms     | ???    |

Sample value: a0 a9 20 ff 00 ff e0 47

* 0, 1, & 2: Change in sync with Power On/Off
* 6 * 7: Counter values


### 0x200

| Bus  | Interval | Sender |
|------|----------|--------|
| PCAN | 10ms     | ???    |

Sample value:  00 80 89 10 00 3b d0 00

Bytes:

* 0: 0x00 in Power Off, 0x44 in Power On
* 1:
  - 0x80 in Park
  - 0xA8 in Drive
  - 0xB0 in Neutral
  - 0xB8 in Reverse
* 2:
  - 0x89 in Power Off
  - When Powering On: goes to 0x00 for a time, then back to 0x89, then keeps 0x67 for rest of Power On
  - When Powering Off: goes direct back to 0x89
* 3:
  - 0x00 in Power Off
  - When Powering On: goes to 0x04, then 0x08, then keeps 0x10 for rest of Power On
  - When Power Off: goes direct to 0x00 again
* 4:
  - 0x00
* 5:
  - Part counter byte, part status bit for power on/off?
* 6:
  - 0xD0
* 7:
  - 0x00

### 0x201

Interval: 10ms
Sender: ????

Sample value: 91 00 00 00 00 00 00 91

Bytes:

* 0 & 7: Counter values? Change each message, mirrored (or garbage bytes)?
* Other bytes all zero?

### 0x202

| Bus  | Interval | Sender |
|------|----------|--------|
| PCAN | 10ms     | ???    |

Sample value: 62 00 20 00 00 00 44 06

* 0: High nibble is counter? Low nibble is 0x0 when Power Off, 0x2 when Power On?
* 1: 0x00
* 2: 0x00 when Power Off, 0x02 when Power On. Transitions a short time after the bit in Byte 0.
* 3,4,5: 0x00
* 6: 0x44 usually, goes to 0x00 for short periods (~500ms) during Power On. Going back to 0x44 at time that Byte 0 low nibble changes 0x0->0x2
* 7: Counter in high nibble. Low nibble is:
  - 0x4 during Power Off
  - Powering on, goes to 0x0 while byte 6 is 0x0 then 0x6
  - Power on: stays 0x6

### 0x220

| Bus  | Interval                  | Sender                                           |
|------|---------------------------|--------------------------------------------------|
| PCAN | 10ms when Power On, only. | Something powered from "IG3" when vehicle is on? |

Some kind of sensor values

* 0,1,2: During Power On cycle seems to transition ff ef ff -> 00 08 00 -> real values
  - Real values look some kind of analog values
* 2,3 seem to be part of one value with 2==MSB,3==LSB

* 7: Counter

### 0x588

| Bus        | Interval                 | Sender |
|------------|--------------------------|--------|
| PCAN, BCAN | 10ms (PCAN), Less (BCAN) | ???    |

This ID seems to have different payload on BCAN vs PCAN, is unchanging(?)

Payload on PCAN is FC FF 03 00 00 00 00 00 (maybe always is this?)

Payload on BCAN is 00 00 00 00 00 00 00 00 when vehicle is Off.





