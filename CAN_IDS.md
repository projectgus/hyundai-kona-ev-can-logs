# CAN IDs

Starting to collect notes on CAN IDs seen in the logs.

## 0x05cc

| Bus  | Interval | Sender |
|------|----------|--------|
| PCAN | 1s     | ???    |

1s interval except when values changes, will sometimes send a flurry of messages.

Sample value: 02 01 00 10 04 00 00 f2

Vehicle off on 2019 has value:

00 00 00 00 00 00 00 f0

Vehicle Ready on 2019 has values like:

02 01 00 10 04 00 00 f2
02 01 01 10 00 00 00 f2

(First sequence seen to transition to the second sequence after some time?)

Vehicle Ready on 2021 has values like:

01 00 81 10 00 08 00 f5
01 10 81 10 00 08 00 f6 -- intermittently these values for 1-2s
01 00 81 10 00 08 00 f6
01 00 81 10 00 08 00 f5

### Commands
`for f in *.log; do echo $f; grep 'ID: 05cc' $f | uniq -s30; done | less `


### 0x07f

IG3 Memory Power Enabled.

| Bus  | Interval                 | Sender |
|------|--------------------------|--------|
| PCAN | 1s, sometimes less often | ???    |

Payload appears to be all zeroes, all the time. (!)

This might be a bug in the Canalyst-II rather than a real capture(!), although not seen elsewhere.

## 0x109

IG3 Memory Power enabled. Probably OBC or BMU.

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
* 3: FF when not charging (see below)
* 4: FF when not charging (see below)
* 5: FF when not charging (see below)
* 6: 0F when not charging (see below)
* 7: counter, seems to change both high and low nibbles each time (two 4-bit counters?)

Bytes 3-6 change occasionally from all-FF during AC charging session (see ac charging log). Doesn't change during fast charge, or at any other time.

❯ grep 00000109 221228-4-2019-ac-charging.csv | cut -d, -f1-12 | uniq -s 39 | less
2644108100,00000109,false,0,8,00,01,00,FF,FF,FF,0F
3080748200,00000109,false,0,8,00,01,00,61,9B,D6,01
3125273600,00000109,false,0,8,00,01,00,61,9B,16,02
3127231300,00000109,false,0,8,00,01,00,61,9B,A2,09
3127900600,00000109,false,0,8,00,01,00,61,9B,A6,0E
3137318900,00000109,false,0,8,00,01,00,61,DB,A4,04
3151960900,00000109,false,0,8,00,01,00,61,9B,A6,0E
3184770600,00000109,false,0,8,00,01,00,61,9B,E6,0E
3201070800,00000109,false,0,8,00,01,00,FF,FF,FF,0F
3252196200,00000109,false,0,8,00,01,00,61,DE,0C,00
3264720800,00000109,false,0,8,00,01,00,61,DE,02,05
3303603300,00000109,false,0,8,00,01,00,61,DE,42,05
3363439800,00000109,false,0,8,00,01,00,61,DE,C2,02
3363529700,00000109,false,0,8,00,01,00,61,DE,02,03

❯ grep 00000109 221228-5-2019-scheduled-charge-session.csv | cut -d, -f1-12 | uniq -s 39 | less
3588264200,00000109,false,0,8,00,01,00,61,A8,0E,00
3843993100,00000109,false,0,8,00,01,00,FF,FF,FF,0F
3845201600,00000109,false,0,8,02,01,00,61,A8,0E,00
3930407200,00000109,false,0,8,00,01,00,B1,A8,0E,00

## 0x10C

IG3 memory power enabled.

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

IG3 memory power enabled.

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

IG3 memory power enabled.

Interval: 10ms
Sender: ????

Sample value: 91 00 00 00 00 00 00 91

Bytes:

* 0 & 7: Counter values? Change each message, mirrored (or garbage bytes)?
* Other bytes all zero?

### 0x202

IG3 memory power enabled.

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

### 0x251

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0x2b0

IG3 memory power enabled.

### 0x333

IG3 memory power enabled.

### 0x371

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0x372

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0x381

IG3 memory power.

### 0x3f6

PCAN. Sent by BMU.

Value mostly 03,00,01,00,00,00,00,00.
Sometimes 04,00,00,00,00,00,00,00
Also occasionally 90,00,01,00,00,00,00,00

Value is all zeroes when BMU is not connected.

### 0x410

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0x412

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0x414

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0x436

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0450

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.


### 045d

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.


### 045e

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.


### 0462

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.


### 0470

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.


### 0471

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.


### 0491

Sent by BMU.

Mostly 00,00,00,00,00,00,00,0F

When fast charge failed:

1D,00,00,00,00,00,00,0E
1D,00,00,00,00,00,00,0F - ten seconds later

Successful DC fast charge (changes):

83490200,00000491,false,0,8,00,00,00,00,00,00,00,0F
100069300,00000491,false,0,8,1F,00,00,00,00,00,00,0F
381285200,00000491,false,0,8,1F,00,0A,00,00,00,00,4F
383981900,00000491,false,0,8,1F,00,18,01,00,00,00,4F
386379200,00000491,false,0,8,1F,00,08,02,00,00,00,4F
523110699,00000491,false,0,8,1F,00,00,00,00,00,00,0F

AC charging session changes, switches every 10-30s:

2650669500,00000491,false,0,8,5A,00,00,00,00,00,00,3F
2750146800,00000491,false,0,8,5A,00,00,00,00,00,00,7F
2760334300,00000491,false,0,8,5A,00,00,00,00,00,00,0F
2788732200,00000491,false,0,8,5A,00,00,00,00,00,00,3F
2825187000,00000491,false,0,8,5A,00,00,00,00,00,00,7F
2830580300,00000491,false,0,8,5A,00,00,00,00,00,00,0F
2857910000,00000491,false,0,8,5A,00,00,00,00,00,00,3F
3201089700,00000491,false,0,8,5A,00,00,00,00,00,00,7F
3233749700,00000491,false,0,8,5A,00,00,00,00,00,00,0F
3251879700,00000491,false,0,8,5A,00,00,00,00,00,00,7F
3252279500,00000491,false,0,8,5A,00,00,00,00,00,00,0F
3256873600,00000491,false,0,8,5A,00,00,00,00,00,00,3F

### 0493

Sent by BMU.

These values vary a bit in normal use, but a lot during AC charging session?

Last 4 bytes probably all zero when off

### 0497

Sent by BMU.

Byte 0 is (sometimes?) an infrequently updating counter 11 -> 14 -> 22 repeating, updating every few seconds (variable?!?)

While driving value seems to stay at 00,00,B4,B3,00,00,00 - has other values when starting up(?)







### 049f

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.


### 04c9

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 04dd

PCAN. Sent by BMU

Only seen two data patterns seen:

* 00,00,00,00,00,00,00,00 (Mostly)
* 00,00,00,00,00,00,01,00 (Sometimes)

Not sure what the 00 and 01 byte correlates to.


### 04fe

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.


### 050b

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 050e

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0520

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0541

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0544

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 054b

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0553

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0559

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 055c

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 055f

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0561

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0567

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0578

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 057f

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0588

IG3 memory power. Could be: OBC, BMS, EPCU. Not EWP.

### 0x588

| Bus        | Interval                 | Sender |
|------------|--------------------------|--------|
| PCAN, BCAN | 10ms (PCAN), Less (BCAN) | ???    |

This ID seems to have different payload on BCAN vs PCAN, is unchanging(?)

Payload on PCAN is FC FF 03 00 00 00 00 00 (maybe always is this?)

Payload on BCAN is 00 00 00 00 00 00 00 00 when vehicle is Off.


ID: 0595
ID: 0596
ID: 05b3
ID: 05be
ID: 05c1
ID: 05d0
ID: 05d3
ID: 05df
ID: 05ec
ID: 05ff
