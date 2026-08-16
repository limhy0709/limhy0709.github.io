---
layout: page
title: NURA Competition — Two-Stage Sounding Rocket
description: Flight computer, staging logic, and telemetry for a two-stage sounding rocket, and a post-flight analysis of why neither flight returned data
img: assets/img/nura/01.png
importance: 2
category: rocketry
---

Competition : NURA (National University Rocket Association), \[2026\]   
Team : Helios, Hanyang University Rocketry Team  
My role : Avionics Team Leader \- Flight computer, Staging logic, Telemetry, Data logging

## **Vehicle**

 A two-stage sounding rocket with a CFRP airframe, flying two in-house aluminium 6061 solid motors. The mass of the airframe was set **7.5 kg for the test flight, lightened to 6.1 kg for the competition** with identical geometry and identical motors.

| Parameter | Value |
| :---: | :---: |
| Liftoff mass | 7.5 kg (test) → 6.1 kg (competition) |
| Length | ≈ 1.82 m  (0.30 m nose \+ 0.95 m stage 2 \+ 0.57 m stage 1\) |
| Airframe | CFRP, 0.6 mm wall |
| Fins | 4 per stage, trapezoidal, 170 mm root / \~95 mm tip / \~90 mm span, 1.5 mm |
| Recovery | 58″ printed nylon parachute |
| Launch rail | 3.0 m, 5° from vertical |

### **Motors — measured static fire, 11 July 2026**

 Both motors share a 60 mm × 261 mm aluminium 6061 case. These are the measured curves, not catalogue values, and they were used directly as the OpenRocket motor definition.

| Parameter | Stage 1 | Stage 2 |
| :---: | :---: | :---: |
| Propellant mass | 0.752 kg | 0.752 kg |
| Burn time | 2.25 s | 1.64 s |
| Peak thrust | 302 N @ 0.92 s | 368 N @ 0.72 s |
| Average thrust | 189 N | 239 N |
| Total impulse | 425 N·s (I) | 391 N·s (I) |
| Delivered Isp | 57.6 s | 53.1 s |

Stage 2 has a 32 mm core, giving it a faster, higher-peak, shorter burn than stage 1 \-  a more progressive grain for the same case.

### **![](/assets/img/nura/01.png) ![](/assets/img/nura/02.png)**

### 

### **Predicted flight (OpenRocket, 4 m/s wind, 5° rail angle)**

| Parameter | 7.5 kg — test | 6.1 kg — competition |
| :---: | :---: | :---: |
| Apogee | 624 m | 794 m |
| **Time to apogee** | **13.27 s** | **14.30 s** |
| Max velocity | 116 m/s (Mach\# 0.34) | 138 m/s (Mach\# 0.40) |
| Max acceleration | 80.2 m/s² (8.2 g) | 89.4 m/s² (9.1 g) |
| Rail exit velocity | 13.2 m/s | 14.9 m/s |
| Deployment velocity | 31.0 m/s | 30.5 m/s |
| Total flight time | 103 s | 136 s |

 Losing 1.4 kg is because the altitude limit is strictly set at the test flight place. It buys 170 m of altitude and moves apogee **one second later**, because the lighter vehicle coasts longer. That one second matters, for reasons in the deployment analysis below.

 Both configurations also carry an unresolved OpenRocket **high-speed deployment warning at \~31 m/s**. The warning was noted and carried into flight rather than designed out.

## **Mission and avionics requirements**

 Unlike a single-stage vehicle, the flight computer here holds **2nd ignition authority** : it decides autonomously when to energize the second-stage igniter, and stage separation follows mechanically from that ignition. Avionics therefore had two jobs — measure and downlink flight data, and make a staging decision in flight.

The system was split across both stages:

| Module | Stage 2 (upper) | Stage 1 (lower) |
| :---: | :---: | :---: |
| MCU | ESP32-DevKitC-32D | \-  |
| IMU | MPU-6050 (I²C) | \- |
| Barometer | BMP280 (I²C) | \- |
| GNSS | NEO-6M (UART2, 9600\) | Air Tag(Apple) |
| Telemetry | Holybro SiK V3 500MW, 915MHz → E22-900T22S1B | \- |
| Storage | microSD | \- |
| Ignition | Relay circuit (BSS138 → SRD-05VDC) | \- |

Stage 1 carried tracking only – GNSS plus a radio, no SD card – so its position existed only as a live downlink.

**Ignition circuit.** The MCU GPIO cannot source nichrome heating current, so the design uses a two-level switch : GPIO → 100 Ω gate resistor → BSS138 N-MOSFET → SRD-05VDC relay coil → relay contacts carry the igniter current. A 1N4148 flyback diode protects against coil back-EMF, and a 10 kΩ gate pulldown keeps the MOSFET off during boot and floating-signal conditions. The high-current path is fully isolated from the logic.

### **PCB Design** 

With the above modules, we designed an appropriate PCB. It is different when it uses at test launch with competition. 

**Changes from the test-flight board**

Ground testing before the test flight found a defect serious enough to force a redesign : **the ignition relay passed current whenever the board was powered**, so the igniter circuit was live the moment the ignition supply was connected.

The coil drive was not the problem. The fault was on the contact side: **the igniter path was taken from the normally-closed(NC) contact instead of the normally-open(NO) one.** The circuit that should have been open by default was closed by default, and energising the coil would have broken it rather than made it. A relay wired this way passes a continuity check and inverts the one thing it exists to guarantee. Soldering jumper wire, we solved this problem, but needed some improvements. 

Version.2(V2) layout removes the ambiguity that allowed it. On the test board the two contact pins were brought straight out as an unlabelled pair of terminals (`RJ1`, `RJ2`), with nothing on the board distinguishing the ignition supply from the igniter. V2 splits them into two dedicated terminal blocks — `HV_IN` brings the ignition supply onto the board, `IGN_OUT` carries current to the igniter, and the relay contact sits between `HV+` and `IGN+`. Supply and load are now separate labelled interfaces, and the igniter can be left disconnected until the vehicle is on the rail.

**Telemetry moved to its own connectors.** V2 gives telemetry two dedicated GH connectors (`TLM1`, `TLM2`), adds a third UART (`RX3` / `TX3`), and breaks IO34 / IO14 out as standalone pads for the E22 link. The radio can now be swapped or removed without disturbing the sensor harness.

|  | Test flight board | competition- flight board |
| :---: | :---: | :---: |
| Igniter path at rest | closed (NC contact) | open until the coil is driven |
| Ignition terminals | one unlabelled pair (RJ1, RJ2) | HV\_IN supply in, IGN\_OUT igniter out |
| Telemetry interface | shared header (H3) | two GH connectors, third UART, IO34/IO14 pads |
| Coil drive | RR1 / RR2 / RD1 / RQ1 | unchanged |
| Module labelling | U5 / H5 / U2 | ESP32 / IMU / STEP\_DOWN |

 Every one of these changes made the board safer in isolation. Taken together they also did something I did not weigh at the time : **they increased the number of mating interfaces in the upper section.** Each connector added is another thing that has to seat correctly, survive vibration, and route without being pinched during final assembly — and the harness compression on launch day happened in exactly that region. Moving current off the board only helps if the resulting connectors are qualified as carefully as the circuit they replaced.

**PCB for Test launch(by chanyoung Kim)**  
![](/assets/img/nura/03.png)

**PCB for competition (by Jonghyeon Na)**   
![](/assets/img/nura/04.png)

**PCB assembly and Avionics mount**

![](/assets/img/nura/05.png)  ![](/assets/img/nura/06.png)

## **Ignition Algorithm**

![](/assets/img/nura/07.png)

Stage 1 Ignition

![](/assets/img/nura/08.png)  
Stage 2 Ignition  
![](/assets/img/nura/09.png)

## **Staging logic — a deliberate simplification**

The initial design called for sensor fusion: IMU burnout detection, barometric altitude gating, a minimum ignition altitude, and a timer window. What flew was a single trigger — a 5-sample trimmed mean of |a|² crossing 290 (m/s²)², about 1.74 g, i.e. **liftoff detection**. Stage 2 is commanded roughly a tenth of a second after the vehicle leaves the rail.

This was a decision, not an oversight, and it turns on the igniter's delay.

|  | Value |
| :---- | :---- |
| Stage 1 burn time | 2.25 s (measured) |
| Nichrome igniter delay | ≈ 1 s |
| Ignition command | at liftoff detection (\~0.1 s) |
| Stage 2 actual light | ≈ 1.1 s — mid stage 1 burn |
| Ignition if triggered on burnout instead | ≈ 3.3 s — coast |

**Why not ignite at burnout.** Detecting burnout and *then* commanding ignition stacks two uncertainties: the detection instant and the \~1 s pyrotechnic delay. Stage 2 would light somewhere around 4 s, well into coast — a regime with falling dynamic pressure, decaying stability margin, and a vehicle increasingly free to weathercock. Ignition and separation in that state is the failure mode I was most worried about.

**Why liftoff detection instead.** Commanding at liftoff puts the actual light-up at \~1.1 s, while stage 1 is still under thrust. The vehicle is at high dynamic pressure, aerodynamically stable, and at low angle of attack — the most forgiving moment available for a separation event. It also removes burnout detection from the critical path entirely: a single threshold with one tunable parameter has far fewer ways to fail than a fusion rule with four, and we had no flight data with which to tune four.

**The trade-off I accepted.** Igniting during stage 1 burn means the motors overlap for roughly two seconds, raising thermal and structural load on the interstage, and making an unsuccessful separation more energetic. I judged that acceptable against coast-phase ignition. **The test flight validated the choice — staging worked.**

**What I would still change:** the design report was never updated to match. The reasoning above lived in my head and in code comments, not in the document the team reviewed. A design decision this consequential should be recorded as a decision, with its rationale and its rejected alternative, at the moment it is made.

## Two other numbers also drifted without being written down — apogee descend count (10 in the report, 7 in code) and the addition of a 15.7 s deployment backup timer. Neither is wrong; neither was tracked.

## **Two builds**

The test flight and the competition flight ran different firmware and, critically, different radios.

|  | Test flight (success) | Competition flight |
| ----- | ----- | :---: |
| Liftoff mass | 7.5 kg | 6.1 kg |
| Radio Telemetry | SiK V3, 915 MHz, **full duplex** | EBYTE E22-900T22S1B LoRa, **half duplex** |
| Radio port | UART0, shared with USB console | UART1 on IO34 / IO14 |
| Downlink rate | 100 ms (10 Hz) | 150 ms (\~6.7 Hz) |
| Packet integrity | none | NMEA-style XOR checksum |
| Velocity estimate | Kalman filter (baro \+ accel) | Kalman filter(only for SD card logging), Finite difference on barometric altitude |
| Accel range | ±8 g | ±16 g |
| BMP280 sampling | ×2 / ×16, IIR ×16 | ×1 / ×4, IIR ×8 |
| Stage-2 relay on-time | 3500 ms | 2000 ms |

### 

### Why each change was made

1. **Radio — SiK Holybro V3 → E22 LoRa.**   
   When test launch, SiK telemetry is constantly disconnected when about 80m fall away. So, I cannot get flight data after 2nd ignition. Also, after test launch recovery failed, I found proper SiK telemetries, but cannot get them until the competition date.   
     I thought E22 could substitute previous SiK telemetry. SiK is **full duplex** : it transmits and receives simultaneously, E22 is **half duplex** while transmitting, its receive circuit is dead.  
    However, LoRa buys range, and the cost is a receive window that has to be budgeted for.

2. **Downlink rate — 100 ms → 150 ms :**  A consequence of the above, at a 19.2 kbps air rate a single packet occupies \~123 ms of air time, so a 100 ms period puts the module at 123% duty. The transmit buffer backs up permanently and the vehicle stops hearing the ground station altogether. 150 ms with GPS on every seventh packet was the compromise that kept most slots open.

3. **Radio port — UART0 → UART1 on IO34/IO14 :**  On the test flight the radio shared UART0 with the USB console, so the two conflicted whenever a cable was attached. Moving to a dedicated UART fixed that. IO34 is input-only with no internal pull-up, so an external 10 kΩ pull-up to 3V3 was added on the board to hold the line in a defined idle state.

4. **Packet integrity — none → XOR checksum :**  On the test flight a corrupted byte reached the ground station and was displayed as if it were data. The competition build appends an NMEA-style XOR checksum so damaged packets can be discarded rather than believed.  
5. **Accelerometer range — ±8 g → ±16 g :**  Required, not optional. Predicted peak acceleration is 8.2 g at 7.5 kg and 9.1 g at 6.1 kg, so ±8 g clips exactly where the data matters. The MPU-6050 on this board is also a clone whose WHO\_AM\_I does not match, which makes `begin()` return false and skips the library's initialisation entirely — leaving the sensor at its ±2 g power-on default. The competition build writes the configuration registers directly and verifies them by read-back.

6. **Barometer sampling — ×2/×16, IIR ×16 → ×1/×4, IIR ×8 :**  The barometer was slower than the control loop. ![](/assets/img/nura/10.png)Two settings decide how long a BMP280 takes to produce one value. *Oversampling* makes the sensor measure several times internally and average the result – cleaner, but slower, in the same way that a longer camera exposure gives a less noisy image at the cost of time.  The *IIR filter* then smooths the output in hardware – steadier, but it lags behind a real change. At ×2/×16 with IIR ×16 a single conversion takes up to 43 ms, while the flight loop reads the sensor every 10 ms. The loop was therefore writing down **the same number four or five times in a row**.   
    Three things followed from that: the 20-sample altitude moving average held only 4–5 genuinely distinct measurements; the finite-difference velocity divided by a `dt` that had not actually elapsed between two different readings; and the IIR lag pushed the detected apogee later than the real one.  Since deployment is judged on that moving average, this set how late recovery could fire. The competition build dropped to ×1/×4 with IIR ×8 — about 13 ms, close enough to the 10 ms loop that nearly every read is fresh.   
    Each sample is slightly noisier, but the hardware filter and the 20-sample average still absorb it: precision traded for time resolution. 

7. **Velocity estimate —Kalman filter(**only for SD card logging**) \+ Finite difference :** The test build fused barometric altitude with accelerometer data into a Kalman estimate and logged it to the SD card. It was never promoted to a deployment criterion, and on reflection I did not think it should be: the MPU-6050's error was large enough that a velocity built on it was not something I wanted a pyrotechnic event to depend on. Rather than carry an estimator that fed no decision, the competition build dropped it and left deployment authority with the barometric moving average plus a timer — fewer inputs, each one I could characterise on the bench.

8. **Stage-2 relay on-time — 3500 ms → 2000 ms.** Holding the relay closed after the igniter has fired only drains the battery and loads the contacts. Worth noting that this constant also gates when apogee detection begins, since `event 3` does not start evaluating until it expires.

Both configurations passed ground testing. The difference only appeared in flight.

 Eight changes went out together on a vehicle that flies once. Three of them were clear improvements, two raised risk, and three followed from other decisions — but with a single flight and no recovered data, none of them can be separated from the others after the fact.

## 

## **Outcome**

**Test flight.** The vehicle flew and the 2nd ignition successfully performed. Parachute is deployed but we couldn’t find the 2nd stage rocket, though the 1st stage rocket was recovered, because the radio module was disconnected after ignition. The airframe was never located, and the log went with it. For 3 days, my team was trying to find the rocket, but we couldn't.  
![](/assets/img/nura/11.png)![](/assets/img/nura/12.png)

![](/assets/img/nura/13.png)![](/assets/img/nura/14.png)

**Competition flight.** Telemetry never came up after final assembly. I noticed it just before launch, and was embarrassed because it worked before assembly. We didn’t have enough time because the competition had a strict time limit. So, I hastily removed software-based arming algorithms, but I couldn’t be sure. At last, the recovery charge did not deploy and the vehicle impacted under ballistic descent. The microSD card was destroyed.

Two flights, no datasets.

## **Failure analysis**

### Symptom A — no telemetry link

I could not isolate this in the field. Several candidates remain, and I now believe more than one was active. After the launch, I discussed why this situation occurred with my team, and selected the most dominant problem.

**A1 — Connector unseating and possible PCB damage.(Leading Candidate)**   
 During an emergency disassembly on launch day I found ESP32 pins bent and partially unseated. Mating the nosecone to the ejection section compresses the harness, and that load appears to have been transferred into the board. Re-seating did not restore the link. Direct physical evidence, and it explains why the link was healthy on the bench and dead only after final integration.

**A2 — The LoRa link was configured outside the module's rated band.**   
 The E22 was set to channel 20, which maps to 850.125 \+ 20 \= **870.125 MHz**. The E22-900T22S1B is rated for **902–928 MHz**. Operating \~32 MHz below the band means the PA, matching network, and antenna are all off-design — a large, silent loss in radiated power and sensitivity. This is a systematic defect that would depress link margin everywhere, and it would be far more forgiving on a bench two metres away than on a pad hundreds of metres out.

**A3 — M0/M1 mode pins not broken out.**   
 The E22's mode pins were not routed to the ESP32, so the module had to be relied upon to sit in transparent mode. If those pins floated, the internal pull-ups place the module in **deep sleep — no transmit, no receive at all.**

**A4 — Antenna mounted flush to CFRP.**   
 Carbon fiber is conductive. The antenna was routed outside the structure but taped directly to the surface, so it was outside the airframe geometrically and not electromagnetically — attenuation plus impedance detuning. A contributing factor, not a sufficient cause on its own.

### 

### Symptom B — no recovery deployment

The deployment logic keys every timer off `ignite_time`, the instant the state machine leaves ARMED:

![](/assets/img/nura/15.png)

Both constants were derived from the **7.5 kg** OpenRocket profile, whose apogee is at 13.27 s: the 10 s lockout closes 3.3 s before apogee, and the 15.7 s backup fires 2.4 s after it. Comfortable margins.

**Neither constant was re-derived when the vehicle was lightened to 6.1 kg.** That configuration reaches apogee at 14.30 s, so the same 15.7 s backup now fires only **1.4 s after apogee** — the margin was cut by more than half, silently, by a mass change made elsewhere in the team. It still would have worked; but a timing constant that depends on the mass properties should not survive a 19% mass change untouched, and nothing in our process linked the two.

There was therefore still a **hard 15.7 s timeout backup**: even with apogee detection failing entirely, deployment should have fired. It did not.

That is the most informative fact from the whole flight. A silent backup timer means the flight computer was not in `event 3` when the timer would have expired — it had either reset, browned out, or never advanced past ARMED. **All of those are consistent with A1**: an intermittent contact that survives a bench test but not launch vibration and 15+ g of boost.

### 

### Root cause chain

Harness routing not treated as an integration constraint

  ![](/assets/img/nura/16.png)  
The original arming chain was **physical arming switch → ground-station ARM command**. With no link, the second half of that chain was unreachable, and I patched the firmware to arm automatically at power-on. That removed the last interlock standing between an unverified vehicle and flight. It is not why telemetry failed; it is why the failure became unrecoverable.

## Why this project matters to me

Before NURA I had never worked on avionics. I came into the project thinking of it as the part of the rocket that records what the rocket does. I came out of it thinking close to the opposite: **a control system is only as good as the physical data path underneath it.** My thresholds were derived and my state machine was correct; neither mattered once a connector under the nosecone stopped making contact. Estimation, tuning, and failure analysis all assume that measurements survive to be read, and that assumption has to be designed for rather than hoped for. Two flights that returned no data at all are what taught me the difference.

The other lesson is less technical, and I take it more seriously. The most consequential decision I made that day was made in a hurry, and it removed a safeguard. In the work I do now, the sensing architecture and the abort logic are the first things I design, not the last.

## Gallery

![](/assets/img/nura/17.png)![](/assets/img/nura/18.png)  
![](/assets/img/nura/19.png)  
![](/assets/img/nura/20.png)![](/assets/img/nura/21.png)

