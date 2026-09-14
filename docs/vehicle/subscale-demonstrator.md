# Phase 1: 1/3-Scale Demonstrator

**Status:** Current goal. Preliminary design, sized from manufacturer data and estimates; **(unv)** marks figures not confirmed on a manufacturer or distributor page, and **(est)** marks engineering estimates. Frozen at the Phase 1 design review.

Last updated: 14 September 2026 · Full-scale reference: [`baseline.md`](baseline.md)

---

## Purpose

An unmanned, roughly 1/3-scale copy of the full-scale vehicle, built to **mirror its architecture as closely as possible** so that what we learn transfers directly:

- the coaxial X8 layout and its coaxial thrust loss
- the flight software, failsafes, and motor-out and pack-out response
- the 4-pack power architecture, pack switching, and hardwired E-stop
- the independent FPGA safety monitor
- the recovery system trigger and deployment
- the test procedures, checklists, and data pipeline used later at full scale
- the club's first custom boards, swapped in one at a time

No person ever rides it. A **2.7 kg ballast** stands in for the pilot.

Entry into Phase 1 flight follows Article X, Section 2: the VP of Engineering, the Chief Safety Officer, and the University Advisor approve in writing after a design review and hazard analysis.

---

## Mirror map

| Full-scale vehicle | 1/3-scale demonstrator |
| --- | --- |
| Coaxial X8, 4 folding arms, rotor spacing 1.1 × prop diameter | Same, with 17 in props: 0.48 m spacing, ~1.1 m overall span |
| 8 × Hobbywing X13 G2 on DroneCAN | 8 × T-Motor MN5008 KV340 with Holybro Kotleta20 **DroneCAN** ESCs |
| 4 independent 18S5P Molicel P50B packs, each powering 2 opposite motors spinning opposite ways | 4 independent **6S1P Molicel P50B** packs (same cell), same pack-to-motor mapping |
| Per-pack contactor, pre-charge, fuse | Per-pack **club-built MOSFET switch board** with pre-charge and fuse |
| Hardwired E-stop opens every contactor | Hardwired E-stop loop disables every pack switch, no software in the path |
| Redundant DC-DC fed from two packs | Two BECs fed from different packs, ORed with ideal diodes |
| 160 lb pilot | 2.7 kg adjustable ballast at the seat position (72.6 kg ÷ 27) |
| Galaxy GRS 3 270 ballistic parachute | Drone parachute sized for ~10 kg with electronic trigger |
| Pixhawk 6X Pro, then club flight controller | Pixhawk 6X, then club flight controller |
| Independent IGLOO2 safety monitor | Same monitor logic on an iCEBreaker, then the club safety monitor board |
| Jetson Orin NX, cameras, OAK-D, lidar (advisory) | Jetson Orin Nano Super, OV9281 pair, OAK-D Pro W, SF45/B (advisory), installed after the airframe is proven |
| Folding arm lock with arm-lock sensor checked at arming | Same, scaled |

### What transfers and what doesn't

| Transfers directly | Does not transfer directly |
| --- | --- |
| Thrust-to-weight margins and motor-out / pack-out control authority (matched ratios) | Controller gains: a 1/3-scale vehicle responds about 1.7× faster (time scales with √length), so gains are retuned at full scale |
| Coaxial thrust loss as a ratio (measured on the thrust stand) | Absolute power, current, and noise |
| Software, mixer, failsafe logic, safety monitor logic, arming logic | Structural loads and vibration frequencies |
| Power architecture behavior: pack loss, switch faults, E-stop | Battery thermal behavior (different pack size) |
| Test procedures, checklists, log analysis | Parachute deployment dynamics |

The demonstrator is **heavier than a true scale model** (9.6 kg vs 6.9 kg for exact 1/3 scaling of 186 kg) because avionics, cameras, and the parachute don't shrink with the airframe. Thrust-to-weight, the ratio that matters most for control authority, is matched instead.

---

## Configuration

| Parameter | Value |
| --- | --- |
| Layout | Coaxial X8: 4 folding arms, upper and lower counter-rotating rotor per arm |
| Propellers | T-Motor P17×5.8 carbon, CW and CCW pairs |
| Rotor spacing | 1.1 × D = 0.48 m between adjacent arm tips |
| Motor-to-motor diagonal | 0.67 m |
| Overall span | ~1.10 m |
| All-up weight | **~9.6 kg** with ballast, parachute, and full payload |
| Bus | 6S (22.2 V nominal, 25.2 V full) |
| Estimated hover power | ~1,200 W (~56 A total, ~14 A per pack) |
| Estimated hover endurance | **~16 min** to 20% charge |

---

## Parts

### Propulsion

| Part | Specs | Mass | Price | Source |
| --- | --- | --- | --- | --- |
| **T-Motor MN5008 KV340** | 6S, P17×5.8: 3,556 g max thrust at 26.4 A (600 W); 986 g at 93 W; 1,224 g at 125 W; 35 A for 180 s | 135 g | $89.99 | [T-Motor](https://store.tmotor.com/product/mn5008-kv340-motor-antigravity-type.html) |
| **T-Motor P17×5.8 carbon props** | CW/CCW pairs | ~25 g (unv) | ~$80 per pair (unv) | T-Motor |
| **Holybro Kotleta20 ESC** | DroneCAN, open-source Sapog firmware, 500 W continuous | 8.8 g | $57.99; $225.99 per 4-pack | [Holybro](https://holybro.com/products/kotleta20) |

Backup ESC: ARK 4IN1 (50 A continuous per motor, DShot with telemetry, no CAN; [ARK](https://arkelectron.com/product/ark-4in1-esc/)). Growth option: MN5008 KV400 (3,982 g max thrust) if weight grows; it exceeds the Kotleta20's 500 W rating at full throttle.

**Kotleta20 limit:** hover (~150 W per motor) and one-motor-out hover (~340 W) are well inside 500 W. Full throttle (600 W) exceeds it, so cap motor output at about 90% in firmware or keep full-throttle bursts brief.

### Power

| Part | Specs | Mass | Price |
| --- | --- | --- | --- |
| **4 × 6S1P Molicel P50B packs** | 5.0 Ah, 60 A continuous per cell; 108 Wh each, 432 Wh total; same cell as full scale ([Molicel](https://www.molicel.com/inr-21700-p50b/)) | ~470 g each with strip, holder, leads (est) | ~$60 each in cells (unv) |
| **Pack switch board** (club-built, ×4) | MOSFET switch with pre-charge, fuse, gate enable from the E-stop loop and the safety monitor, current and voltage sense | ~30 g each (est) | ~$35 each (est) |
| **Holybro PM08-CAN** | 14S, 200 A power module on CAN | ~50 g (unv) | $83 ([Holybro](https://holybro.com/collections/power-modules-pdbs)) |
| **Holybro UBEC 12A** + **UBEC 5A** | Avionics and Jetson supply, fed from two different packs through ideal-diode ORing | ~25 g each | $36.59 + $20.99 |
| XT60 per pack, arming loop key, E-stop switch, harness | Physical arming and hardwired E-stop | ~300 g (est) | ~$120 (est) |

**Per-pack current check:** hover ~14 A per pack; two motors at full throttle ~53 A, under the 60 A cell rating; with one pack out, each remaining pack carries ~24 A in hover.

### Flight stack

| Part | Specs | Mass | Price |
| --- | --- | --- | --- |
| **Holybro Pixhawk 6X** + mini baseboard | STM32H753, triple IMU, Ethernet to the Jetson | 58 g | $286.98 with PM02D (**sold out** at time of search) ([Holybro](https://holybro.com/products/pixhawk-6x)) |
| Holybro M10 GPS | GNSS and compass | ~40 g (unv) | $43.99 ([Holybro](https://holybro.com/products/m10-gps)) |
| Holybro SiK Telemetry V3 915 MHz | Telemetry | 23.5 g | $58.99 ([Holybro](https://holybro.com/products/sik-telemetry-radio-v3)) |
| ExpressLRS receiver (e.g. RadioMaster RP3) | RC link | ~5 g (unv) | ~$20 (unv) |
| Remote ID broadcast module | FAA 14 CFR 89 | ~10 g (unv) | ~$50 (unv) |
| ARK Flow | Optical flow and distance | ~10 g (unv) | $250 ([ARK](https://arkelectron.com/product/ark-flow/)) |
| iCEBreaker (safety monitor prototype) | iCE40UP5K | ~20 g (unv) | $79.95 |

### Recovery

| Part | Notes | Mass | Price |
| --- | --- | --- | --- |
| Drone parachute system for ~10 kg, electronic trigger | Candidates: Fruity Chutes drone recovery kits, Mars Parachutes, ParaZero SafeAir, Galaxy GBS series. **Not yet researched in detail**; confirm weight rating, minimum deployment height, and trigger interface | ~700 g (est) | ~$1,000 (est) |

### Perception payload (second build stage)

Same parts as the [subscale perception BOM](avionics-parts.md#perception-boms): Jetson Orin Nano Super, 2 × OV9281, OAK-D Pro W, LightWare SF45/B, SIYI HM30. About $2,700 and ~570 g. Flown only after the airframe is proven; a dummy mass of the same weight and position replaces it for early flights.

---

## Airframe

**Build, don't buy.** Commercial X8 frames (e.g. Tarot X8, ~1,050 mm wheelbase, ~1.6 kg, ~$350, unv) don't match the full-scale arm geometry, fold hinge, seat position, or landing gear. A club-built frame lets Mechanical practice the same design decisions at low cost.

| Element | Design |
| --- | --- |
| Arms | 4 × 25 mm carbon tubes, ~0.42 m, at the full-scale arm angles |
| Fold hinge and lock | Scaled version of the full-scale hinge, with an arm-lock sensor wired to the flight controller |
| Coaxial motor mounts | Upper motor on top, lower motor inverted underneath, same relative gap (in prop diameters) as full scale |
| Centre structure | Carbon plates standing in for the 4130 cage; 4 pack bays in the same positions as full scale |
| Ballast tray | 2.7 kg nominal, adjustable ±4 cm fore-aft and side to side on a slotted rail, at the seat position |
| Landing gear | Scaled skids |
| Parachute mount | Same location as full scale |
| Estimated mass | ~1.2 kg frame and gear, ~150 g ballast tray (est); ~$350 in materials (est) |

---

## Mass budget

| Item | Qty | Unit g | Total g |
| --- | ---: | ---: | ---: |
| Carbon frame, folding arms, coaxial mounts, landing gear (est) | 1 | 1,200 | 1,200 |
| Ballast tray and rail (est) | 1 | 150 | 150 |
| **Pilot ballast** | 1 | 2,700 | 2,700 |
| MN5008 KV340 motors | 8 | 135 | 1,080 |
| P17×5.8 props (unv) | 8 | 25 | 200 |
| Kotleta20 ESCs | 8 | 8.8 | 70 |
| 6S1P P50B packs (est) | 4 | 470 | 1,880 |
| Pack switch boards (est) | 4 | 30 | 120 |
| Power harness, XT60s, loop key, E-stop (est) | 1 | 300 | 300 |
| CAN harness | 1 | 30 | 30 |
| PM08-CAN (unv) | 1 | 50 | 50 |
| BECs | 2 | 25 | 50 |
| Pixhawk 6X and mini baseboard | 1 | 58 | 58 |
| M10 GNSS (unv) | 1 | 40 | 40 |
| SiK telemetry | 1 | 24 | 24 |
| ELRS receiver (unv) | 1 | 5 | 5 |
| Remote ID module (unv) | 1 | 10 | 10 |
| ARK Flow (unv) | 1 | 10 | 10 |
| iCEBreaker safety monitor (unv) | 1 | 20 | 20 |
| Club sensor boards (allowance) | 1 | 100 | 100 |
| Parachute system (est) | 1 | 700 | 700 |
| Jetson Orin Nano Super | 1 | 175 | 175 |
| OV9281 cameras (unv) | 2 | 15 | 30 |
| OAK-D Pro W | 1 | 91 | 91 |
| SF45/B | 1 | 59 | 59 |
| HM30 air unit (unv) | 1 | 110 | 110 |
| Mounts, dampers, antennas, cables (est) | 1 | 200 | 200 |
| Payload deck and canopy (est) | 1 | 150 | 150 |
| **All-up weight** | | | **9,612** |

---

## Thrust and endurance

Coaxial factor 0.85 on every rotor, as in the full-scale baseline. "Sagged" assumes a pack near the end of discharge (~21 V), with thrust × 0.77.

| Case | Thrust | Thrust-to-weight | Sagged pack |
| --- | ---: | ---: | ---: |
| All 8 rotors | 24.2 kgf | **2.52** | 1.94 |
| One motor out | 21.2 kgf | **2.20** | 1.69 |
| One pack out (6 rotors) | 18.1 kgf | **1.89** | 1.45 |

Full scale: 2.19 / 1.92 / 1.65. **The demonstrator matches or exceeds full-scale margins with full packs.** Near the end of a discharge it drops below 2:1, so the flight envelope ends motor-out and pack-out tests with a charge margin, and the low-battery failsafe lands before the pack sags that far. If the mass grows, the MN5008 KV400 restores margin (needs higher-power ESCs).

**Hover:** ~1.20 kgf per rotor → ~122 W per rotor from the MN5008 table, +18% coaxial → ~1,150 W for 8 rotors, plus ~40 W avionics and 3% wiring → **~1,200 W (~56 A)**.

**Endurance:** 432 Wh × 80% usable × 95% efficiency = 328 Wh → **~16 min** hover. Without the perception payload, ~17 min.

---

## Build stages

| Stage | Configuration | Exit criteria |
| --- | --- | --- |
| **0. Simulation** | PX4 or ArduPilot software-in-the-loop, coaxial X8 model with this mass and thrust | Mixer, failsafes, motor-out and pack-out cases behave correctly in simulation |
| **1. Bench** | Thrust stand: single MN5008, then a coaxial pair; pack switch boards; E-stop loop | Coaxial factor measured; switch and E-stop tests pass; pack logging works |
| **2. Airframe flying** | Full airframe, flight stack, 4-pack power, parachute, **dummy mass instead of perception payload** | Stable tethered then free hover; tuned; motor-out and pack-out response demonstrated |
| **3. Safety systems proven** | iCEBreaker safety monitor active; parachute trigger tested | Monitor trips correctly on heartbeat loss and limit exceedance (on tether); parachute deployment verified |
| **4. Perception installed** | Jetson, cameras, OAK-D, lidar, video link replace the dummy mass | VIO and datasets logged against flight controller estimates; obstacle warnings working |
| **5. Club boards** | Club-built sensor nodes, flight controller, safety monitor board swapped in one at a time | Each board passes bring-up, then logs flight time alongside or instead of the commercial part |

---

## Test plan

Every powered test runs under a written procedure and a Test Readiness Review signed by the Chief Safety Officer (Article X, Section 4). These are the same test types planned for the full-scale vehicle.

| Test | What it proves | Full-scale counterpart |
| --- | --- | --- |
| Motor thrust stand, single rotor | Motor, prop, ESC data vs catalog | X13 G2 characterization |
| Coaxial pair on thrust stand | Actual coaxial thrust and power factor vs the 0.85 / +18% assumption | Coaxial spacing study |
| Pack switch and pre-charge bench test | Switching, inrush, fault response | Contactor and pre-charge qualification |
| E-stop loop test | All packs open with no software | Hardwired E-stop verification |
| Restrained and tethered hover | Control loop stability, vibration, logging | Phase 2 tether campaign |
| Free hover and tuning | Attitude and position control, EKF health | Full-scale tuning |
| **Motor-out in flight** | Controlled hover and landing with one motor disabled | Article X, Section 5(d) motor-out demonstration |
| **Pack-out in flight** | Controlled landing with one pack switched off (two opposite motors) | Pack-out requirement |
| Safety monitor trip (on tether) | Heartbeat loss and limit exceedance cut power as designed | Safety monitor qualification |
| Parachute deployment | Trigger and deployment at a safe height | Recovery system verification |
| Endurance and thermal | Pack temperatures, ESC and motor temperatures, flight time | Pack thermal test |
| Perception flights | VIO and mapping accuracy vs GNSS and flight controller | Companion computer validation |

**Test site:** open, off-campus, with written site permission, an exclusion zone, a safety observer, and fire suppression for lithium packs (Article X, Sections 6 and 7).

---

## Regulatory and University checklist

- [ ] **Operating rules.** Test flights for an aircraft development program are unlikely to qualify as recreational under [49 USC 44809](https://www.law.cornell.edu/uscode/text/49/44809) (unv), so plan to fly under **14 CFR Part 107** with a certificated remote pilot in command.
- [ ] **Remote pilot certificate** for every pilot in command (FAA knowledge test).
- [ ] **Registration** on FAADroneZone (over 250 g, under 55 lb); number marked on the outside.
- [ ] **Remote ID:** broadcast module with its serial number in the registration, or fly only inside an FAA-recognized identification area ([14 CFR 89](https://www.ecfr.gov/current/title-14/part-89)).
- [ ] **Airspace:** LAANC or other authorization in controlled airspace; visual line of sight; at or below 400 ft AGL; check airspace near Austin-Bergstrom and stadium event restrictions.
- [ ] **UT Austin:** no drone policy was found in the Handbook of Operating Procedures (unv). Ask the University Policy Office (policyoffice@austin.utexas.edu) and Risk Management which approvals apply to a student organization, and get written approval from the University Advisor for each site.
- [ ] **Texas law:** review Texas Government Code chapter 423 on drone imaging before flying cameras (unv).
- [ ] **Lithium batteries:** charging and storage approved by UT Environmental Health and Safety (Article X, Section 7).

---

## Cost

| Item | Cost |
| --- | ---: |
| MN5008 KV340 × 10 (2 spares) | $900 |
| P17×5.8 props × 6 pairs (unv) | $480 |
| Kotleta20 ESCs, 2 × 4-pack | $452 |
| Pixhawk 6X, mini baseboard, PM02D | $287 |
| PM08-CAN | $83 |
| M10 GNSS, SiK telemetry, ELRS receiver, Remote ID (partly unv) | $173 |
| BECs and ideal-diode ORing | $98 |
| P50B cells for 2 flight sets (8 × 6S1P) plus charger (unv) | $700 |
| Pack switch boards × 5 (est) | $175 |
| Frame materials (est) | $350 |
| Ballast, harness, connectors, E-stop, loop key (est) | $120 |
| Drone parachute system (est) | $1,000 |
| ARK Flow | $250 |
| iCEBreaker | $80 |
| **Airframe, power, and flight stack subtotal** | **≈ $5,150** |
| Tyto Robotics Series 1585 thrust stand (unv) | ≈ $1,600 |
| Perception payload ([subscale BOM](avionics-parts.md#perception-boms)) | ≈ $2,700 |
| **Phase 1 total** | **≈ $9,450** |

The perception payload is stage 4 and can be funded separately. **Getting a flying, safety-complete demonstrator costs about $6,750** including the thrust stand.

---

## Open items

1. Select and verify the drone parachute (weight rating, minimum deployment height, electronic trigger).
2. Confirm prop masses and P17×5.8 availability; consider P18×6.1 for closer geometric scale once thrust data exists.
3. Design the pack switch board (MOSFET selection, pre-charge, gate drive, current sense) as the club's first power board.
4. Confirm Pixhawk 6X stock or order the 6C as a stopgap.
5. Confirm UT approvals and a test site.
6. Get a Tyto Robotics quote, including a stand that can take a coaxial pair (~7 kgf).

## Sources

- Motors: [MN5008 KV340](https://store.tmotor.com/product/mn5008-kv340-motor-antigravity-type.html), [MN5008 KV400](https://store.tmotor.com/product/mn5008-kv400-motor-antigravity-type.html)
- ESCs: [Holybro Kotleta20](https://holybro.com/products/kotleta20), [ARK 4IN1](https://arkelectron.com/product/ark-4in1-esc/)
- Flight stack and power: [Pixhawk 6X](https://holybro.com/products/pixhawk-6x), [M10 GPS](https://holybro.com/products/m10-gps), [SiK V3](https://holybro.com/products/sik-telemetry-radio-v3), [Holybro power modules](https://holybro.com/collections/power-modules-pdbs)
- Battery: [Molicel P50B](https://www.molicel.com/inr-21700-p50b/)
- Thrust stand: [Tyto Robotics Series 1580/1585](https://www.tytorobotics.com/pages/series-1580-1585)
- Regulatory: [49 USC 44809](https://www.law.cornell.edu/uscode/text/49/44809), [14 CFR Part 89](https://www.ecfr.gov/current/title-14/part-89), [UT University Policy Office](https://compliance.utexas.edu/university-policy-office/)
