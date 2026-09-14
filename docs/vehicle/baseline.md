# Vehicle Baseline v1

**Status:** Preliminary. The numbers come from manufacturer data where it exists and from engineering estimates where it does not; estimates are marked. Thrust stand testing replaces them, and the baseline is frozen at the Preliminary Design Review (PDR). Changes after PDR go through configuration management.

Last updated: 14 September 2026 

---

## 1. Design point

| | Value |
| --- | --- |
| Pilot | 160 lb (72.6 kg), equipped |
| Regulatory basis | 14 CFR Part 103: empty weight < 254 lb (115.2 kg), batteries included |
| Thrust requirement | **2 × gross weight**, not 2 × pilot weight. Thrust has to lift the vehicle, batteries, and pilot, then have the same again in reserve for control and gusts. |
| Gross weight | ≈ 186 kg (410 lb) |
| Required total thrust | ≈ 372 kgf (820 lb) |

### A Part 103 

[103.1(e)(1)](https://www.law.cornell.edu/cfr/text/14/103.1) sets the limit at 254 lb empty weight "excluding floats and safety devices which are intended for deployment in a potentially catastrophic situation." A ballistic parachute is a strong candidate for that exclusion (LIFT Aircraft's HEXA has been reported to take it), **something that we are not entirely sure of need FAA check**

---

## 2. Configuration

**Coaxial octocopter (X8).** Four folding arms, each with an upper and lower counter-rotating rotor.

- A single motor failure leaves its partner on the same arm, so thrust stays roughly symmetric.
- Folding arms solve transport: agricultural drone arms are built to fold.
- Jetson ONE, the closest flying reference, uses the same layout at 115 kg with batteries ([source](https://evtol.news/jetson-one-production-model)).
- ArduPilot (OctaQuad X) and PX4 (Octorotor Coaxial) both support the frame ([ArduPilot](https://ardupilot.org/copter/docs/frame-type-configuration.html), [PX4](https://docs.px4.io/main/en/airframes/airframe_reference)).

Coaxial cost: the lower rotor runs in the upper rotor's wake. Planning figures: **0.85 × thrust per rotor, +18% hover power** ([Rutgers thesis](https://rucore.libraries.rutgers.edu/rutgers-lib/55491/); [Springer 2016](https://link.springer.com/chapter/10.1007/978-3-319-29357-8_46)).

---

## 3. Propulsion: two options

| | **Option B (recommended)** | Option A |
| --- | --- | --- |
| Unit | [Hobbywing X13 G2](https://www.hobbywing.com/en/products/x13-g2) integrated motor, ESC, and prop | [T-Motor U15L](https://store.tmotor.com/product/u15l-manned-aircraft-uav-motor.html) KV43 + NS47×18 prop + separate ESC |
| Bus | 18S (64.8 V nominal, 75.6 V full) | 24S (86.4 V nominal, 100.8 V full) |
| Prop | 56 in folding (MFP56×20, 811 g) | 47 in fixed (NS47×18, ≈ 640 g) |
| Mass per rotor | 4.19 kg (motor, ESC, prop) | 3.60 motor + 0.64 prop + 0.46 ESC = 4.70 kg |
| Max thrust per rotor | 60 kgf (manufacturer) | 63.4 kgf at 98 V, ≈ 53 kgf at pack nominal (estimate) |
| Hover data point | 27 kgf at 8.6 g/W | 24.8 kgf at 3,279 W (7.6 g/W) |
| Continuous rating | 2,700 W; ESC 70 A continuous, 200 A for 3 s | Motor 135 A for 120 s |
| Price per rotor | ≈ $475 (G1 price; G2 unverified) | ≈ $2,700 plus ESC |
| Footprint | ≈ 3.0 m square, 3.6 m diagonal (folds) | ≈ 2.3 m square |
| Thrust-to-weight, all / 1 motor out / 1 pack out | **2.19 / 1.92 / 1.65** | 1.89 / 1.65 / 1.42 |
| Empty weight, with chute | **250 lb** | 261 lb, over the limit |

**Why B.** It is the only option that meets 2:1 thrust-to-weight and stays under 254 lb with the parachute counted. It also costs about one fifth as much. The lower bus voltage gives ESCs, BMS chips, and DC-DC converters real voltage margin, and it makes a student-built ESC practical on open hardware.

**The open risk with B.** On motor-out, the surviving rotor on that arm must hold roughly 40 to 45 kgf for 60 to 120 s until landing. That likely needs 110 to 120 A (estimate), against an integrated ESC rated 70 A continuous. **Resolve this first:** get Hobbywing's thermal limit at that load in writing, then verify it on the thrust stand. If it fails, fall back to Option A with a lighter airframe.

### ESCs if the ESC is separate (Option A, or a later custom build)

| ESC | Voltage | Continuous / peak | Mass | Interface |
| --- | --- | --- | --- | --- |
| [MGM COMPRO HBCi 200120](https://mgm-compro.com/controllers-inverters-escs/hbci-200120/) | 32 to 120 V | 200 A / 27 kW short-term | 458 g | CAN, RS485, PWM |
| [Hobbywing XRotor H300A 24S FOC](https://www.hobbywingdirect.com/products/eps-esc-foc-h300) | 36 to 130 V | 140 / 300 A | 800 g | PWM + CAN |
| [MAD AMPX 200A HV](https://mad-motor.com/products/mad-ampx-esc-200a-12-24s-hv) | 40 to 100 V | 200 / 300 A (claimed) | unverified | DroneCAN |

Many "200 A" 24S ESCs are only 85 to 100 A continuous (T-Motor V200A, Hobbywing H200A). Check continuous ratings, not headline numbers.

---

## 4. Custom ESC: designing our own on existing chips

We can build our own ESC PCB. It does **not** save mass (≈ 350 to 500 g, same as COTS), and it puts the highest-energy failure mode on student hardware, so it follows Article X, Section 3: design review, current-limited bring-up, qualification at or above operating conditions, and logged run time on the unmanned article. Path: **dyno → subscale → full-scale unmanned.** Not in the piloted flight path this program cycle.

Reference design for an 18S, 150 A continuous / 250 A peak FOC inverter:

| Block | Part | Why |
| --- | --- | --- |
| MOSFETs | Infineon [IPT025N15NM6](https://www.infineon.com/part/IPT025N15NM6) (150 V, 2.5 mΩ, TOLL), 2 in parallel per switch, 12 total; TOLT [IPTC034N15NM6](https://www.infineon.com/part/IPTC034N15NM6) for top-side cooling | 2× voltage margin over a 75.6 V bus |
| Gate driver | Infineon [6ED2742S01Q](https://www.infineon.com/cms/en/product/power/gate-driver-ics/6ed2742s01q/) (160 V three-phase, built-in overcurrent trip, bootstrap diodes) | TI DRV8353 is 102 V abs max, too close |
| Phase current | Allegro [ACS772](https://www.allegromicro.com/en/products/sense/current-sensor-ics/integrated-current-sensors/acs772) isolated Hall, 400 A range | INA240 (80 V) and INA241 (110 V) are too tight |
| MCU | STM32F405 (runs VESC and MESC unmodified) or STM32G474 | Existing FOC firmware |
| CAN | TI TCAN1044, or ISO1042 isolated | DroneCAN to the flight controller |
| Bus capacitors | 100 V+ polymer bank (≈ 1 to 2 mF) plus X7R ceramics at each half-bridge | |
| Starting point | [Trampa VESC 100/250](https://trampa.co.uk/product/vesc-100-250/) (safe to 22S, 250 A) as the dyno platform; [MP2](https://github.com/badgineer/MP2-ESC) open design as the layout reference | 18S sits inside both |

Key risks: voltage ringing at switching edges, current sharing across paralleled FETs, gate drive layout, hardware short-circuit trip independent of the MCU, and firmware behavior after a sensorless desync.

---

## 5. Energy storage

| Parameter | Baseline |
| --- | --- |
| Cell | [Molicel P50B](https://www.molicel.com/inr-21700-p50b/) 21700: 5.0 Ah, 3.6 V, 60 A continuous, ≈ 71 g, 12.8 mΩ DC |
| Alternates | Molicel P60B (6.0 Ah, 90 A, availability limited), EVE 40PL (4.0 Ah, 70 A) |
| Architecture | 4 independent packs, **18S5P** each (90 cells) |
| Pack voltage | 64.8 V nominal, 75.6 V full, ≈ 54 V cutoff |
| Energy | 1.62 kWh per pack, **6.5 kWh total** |
| Pack mass | ≈ 9.6 kg (cells 6.4 kg plus busbars, holders, enclosure, contactor, fuse, BMS, pack harness; overhead is an estimate) |
| Hover power | ≈ 24 to 27 kW total (from X13 G2 data with coaxial penalty) |
| Cell current at hover | ≈ 20 A (≈ 5 W heat per cell) |
| Endurance | ≈ 12 min to 20% charge by energy. Plan on about 8 min until pack thermal tests confirm more. |
| Peak | Full-thrust bursts pull ≈ 70 A per cell, above the continuous rating, so they are limited to seconds |

**Pack-to-motor mapping.** Each pack powers two motors on opposite arms that spin in opposite directions. Losing a pack removes two motors without a yaw imbalance, and the remaining six still give thrust-to-weight ≈ 1.65.

**Before choosing a cell:** buy about 10 each of P50B, P60B, and 40PL, and measure 10 s pulse resistance at 20 to 50% charge. That number decides the pack size.

### Per-pack protection

| Function | Part | Mass |
| --- | --- | --- |
| Main contactor | [Gigavac GX14](https://www.sensata.com/sites/default/files/a/sensata-gigavac-gx14-series-open-contactors-datasheet.pdf), 350 A | 0.50 to 0.56 kg |
| Pre-charge | Small relay plus pulse resistor, ≈ 1 s | ≈ 50 g (est.) |
| Fuse | Mersen MEV or Bussmann EV fuse, ≈ 350 A class | ≈ 0.1 kg (est.) |
| BMS | Custom board, one [ADI ADBMS1818](https://www.analog.com/en/products/adbms1818.html) (18S, isoSPI) per pack; or COTS [Orion BMS 2](https://www.orionbms.com/downloads/documents/orionbms2_specifications.pdf) (1.13 kg) | ≈ 0.1 kg custom |
| Connectors | Amphenol SurLok Plus 8 mm (200 A) or bolted lugs; AS150U and AS250 are too small | |
| Motor branch wire | 6 AWG silicone, ≈ 145 g/m | |

### Low-voltage power

| Option | Part |
| --- | --- |
| COTS | [Vicor DCM3623](https://www.vicorpower.com/documents/datasheets/DCM3623xA5N31B4y7z_ds.pdf), 43 to 154 V in, 320 W, 24 g, isolated. Two units fed from different packs, ORed with ideal diodes. |
| Custom | TI LM5164 (100 V) or ADI [LTC7801](https://www.analog.com/en/products/ltc7801.html) (150 V) buck to 12 V, then a 12 V to 5 V stage. An 18S bus leaves margin that 24S would not. |

The bus is above 60 V DC and treated as hazardous voltage: hardwired E-stop opens every pack contactor, orange cabling, touch-safe connectors, and no energized bench work with props installed.

---

## 6. Avionics and computing (Article X, Section 10)

| Function | Part | Mass |
| --- | --- | --- |
| Flight controller | [Holybro Pixhawk 6X Pro](https://docs.holybro.com/autopilot/pixhawk-6x-pro/technical-specification) (triple IMU incl. ADIS16470, STM32H753) or [Cube Orange+](https://docs.px4.io/main/en/flight_controller/cubepilot_cube_orangeplus) | ≈ 100 g |
| GNSS | 2× [CubePilot Here4](https://www.readymaderc.com/products/details/85804-here4-multiband-rtk-gnss) (u-blox F9P) | 120 g |
| Altitude | [LightWare LW20/C](https://www.digikey.com/en/product-highlight/l/lightware-lidar/lw20-c-microlidar-distance-sensor) LiDAR, 100 m | 19 g |
| Telemetry | [RFD900x](https://irlock.com/products/rfd-900x-modem) | 15 g |
| Safety monitor | Prototype: [iCEBreaker](https://1bitsquared.com/products/icebreaker) (iCE40UP5K). Flight unit: Lattice [MachXO3D](https://www.latticesemi.com/en/Products/FPGAandCPLD/MachXO3) or Microchip IGLOO2, both with AEC-Q100 versions, flash-based, instant-on. Own supply and reset domain. | ≈ 300 g with supply and enclosure |
| Companion computer | AMD [Kria K26](https://www.crowdsupply.com/amd/amd-kria-tm-k26-som-and-kits) SOM (Zynq UltraScale+), advisory only | ≈ 500 g with carrier and heatsink |
| Motor-out handling | PX4 detects failure from ESC current telemetry ([docs](https://docs.px4.io/main/en/config/safety)); ArduPilot's thrust-loss check is a heuristic only | |

---

## 7. Recovery, seat, and structure

| Item | Part | Mass |
| --- | --- | --- |
| Ballistic parachute | [Galaxy GRS 3 270](https://www.galaxysky.cz/grs-3-270-60m2-p58-en) (60 m², 270 kg max) plus mount | 9.5 kg |
| Chute limitation | Published minimum is 40 m at 65 km/h forward speed. From a hover it needs more height, which shapes the flight envelope. | |
| Seat | Carbon bucket shell | ≈ 3.0 kg |
| Restraint | 4-point harness | ≈ 2.5 kg |
| Controls | Hall-effect stick, throttle, E-stop, mounts | ≈ 1.8 kg |
| Centre cage | 4130 chromoly, ≈ 12 m of 1 in × 0.035 in tube ([0.537 kg/m](https://www.aircraftspruce.com/catalog/mepages/4130tubing_un1.php)) plus gussets | ≈ 7.2 kg |
| Arms | 4 folding carbon tubes, 60 mm × 2 mm wall (≈ 0.54 kg/m), braces, fold joints | ≈ 7.3 kg |
| Landing gear | 4130 or CF skids | ≈ 3.0 kg (est.) |

---

## 8. Mass budget (Option B)

| Item | kg | lb |
| --- | ---: | ---: |
| Airframe: centre cage, folding arms, joints | 14.5 | 32.0 |
| Landing gear | 3.0 | 6.6 |
| Coaxial motor mounts (4) | 2.0 | 4.4 |
| Propulsion units, 8 × X13 G2 (motor, ESC, prop) | 33.5 | 73.8 |
| Battery packs, 4 × 18S5P with protection and BMS | 38.6 | 85.0 |
| HV distribution and E-stop contactor | 1.0 | 2.2 |
| Avionics, safety monitor, companion, LV power | 4.0 | 8.8 |
| Seat, restraint, controls | 7.3 | 16.1 |
| Ballistic parachute | 9.5 | 20.9 |
| **Empty weight, parachute counted** | **113.3** | **249.9** |
| **Empty weight, parachute excluded under 103.1(e)(1)** | **103.8** | **228.9** |
| Part 103 limit | 115.2 | 254.0 |
| Pilot | 72.6 | 160.0 |
| **Gross** | **185.9** | **409.9** |

| Margin | kg | lb | % |
| --- | ---: | ---: | ---: |
| Parachute counted | 1.9 | 4.1 | 1.6 |
| Parachute excluded | 11.4 | 25.1 | 9.9 |

**Thrust check.** 8 × 60 kgf × 0.85 coaxial factor = 408 kgf against 186 kg gross, a thrust-to-weight of **2.19**. The 2:1 target needs 372 kgf, so there is 36 kgf (≈ 80 lb) to spare.

---

## 9. Rough cost (hardware only, unverified prices)

| Item | Estimate |
| --- | --- |
| 10 × X13 G2 (8 plus 2 spares) | ≈ $4,800 |
| ≈ 400 P50B cells (including spares and test cells) | ≈ $4,000 |
| 4 × contactors, fuses, pre-charge, BMS boards | ≈ $2,000 |
| Flight controller, GNSS, LiDAR, radios, safety monitor, companion | ≈ $2,500 |
| Ballistic parachute | ≈ $4,000 to 6,000 (unverified) |
| Airframe materials, seat, harness | ≈ $3,000 |
| **Total** | **≈ $20,000 to 22,000** |

Option A would add roughly $30,000 in motors and props alone.

---

## 10. Subscale demonstrator (Phase 1)

| Parameter | Target |
| --- | --- |
| Configuration | Coaxial X8, about 1/3 scale |
| Propellers | 15 in |
| Battery | 6S LiPo |
| All-up mass | ≈ 5 kg |
| Flight controller | Same family as full scale |
| Safety monitor | Same RTL on an iCEBreaker |
| Low-voltage power | Team-built buck converter board (6S to 5 V) |

---

## 11. Open items, in priority order

1. **X13 G2 motor-out thermal limit** at 40 to 45 kgf for 120 s: ask Hobbywing, then thrust stand test.
2. **FAA reading on parachute exclusion** from empty weight.
3. **Cell pulse resistance test**: P50B vs P60B vs 40PL.
4. **Pack thermal test** at hover current with downwash cooling.
5. **Coaxial rotor spacing** test on the thrust stand (closing the gap from 8 in to 2 in gained 4.5% in the Rutgers study).
6. **Airframe FEA** to confirm the 14.5 kg structure estimate.
7. **Parachute deployment height** from a hover.

## Method notes

- Hover power per rotor comes from manufacturer thrust and efficiency points (g/W), scaled by +18% for coaxial interaction. v0 used momentum theory on the combined disk area and then added a coaxial penalty on top, which counted it twice.
- Ideal hover power per isolated rotor: P = T^1.5 / sqrt(2 ρ A), with ρ = 1.225 kg/m³.
- Energy: cells × 5.0 Ah × 3.6 V. Usable energy to 20% charge.
- Masses marked "est." are engineering estimates, not measurements.
