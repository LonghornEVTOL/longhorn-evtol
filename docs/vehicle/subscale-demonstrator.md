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
| Coaxial X8, 4 folding arms, 56 in props, rotor spacing 1.1 × D | Same layout with 18 in props (56 ÷ 3 ≈ 18.7 in) at the same 1.1 × D spacing |
| 3.64 m span, 2.21 m motor diagonal | ~1.17 m span, 0.71 m motor diagonal |
| 8 × Hobbywing X13 G2 on a DroneCAN motor bus | 8 × T-Motor MN5008 KV340 with Zubax Myxa **DroneCAN** ESCs |
| 4 independent 18S5P Molicel P50B packs, each powering 2 opposite motors spinning opposite ways | 4 independent **6S1P Molicel P50B** packs (same cell), same pack-to-motor mapping |
| Per-pack contactor, pre-charge, fuse | Per-pack **club-built MOSFET switch board** with soft-start pre-charge and fuse |
| Hardwired E-stop opens every contactor | Hardwired E-stop loop disables every pack switch, no software in the path |
| Redundant DC-DC fed from different packs | BECs fed from all 4 packs through ideal-diode ORing |
| 160 lb pilot | 2.7 kg adjustable ballast at the seat position (72.6 kg ÷ 27) |
| Galaxy GRS 3 270 ballistic parachute | Fruity Chutes Skycat 10 kg parachute with electronic trigger |
| Pixhawk 6X Pro, then club flight controller | Pixhawk 6X, then club flight controller |
| Independent IGLOO2 safety monitor | Same monitor logic on an iCEBreaker, then the club safety monitor board |
| Jetson Orin NX, cameras, OAK-D, lidar (advisory) | Jetson Orin Nano Super, OV9281 pair, OAK-D Pro W, SF45/B (advisory), installed after the airframe is proven |
| Folding arm lock with arm-lock sensor checked at arming | Same, scaled |
| Upper-to-lower rotor gap | Same gap as a fraction of prop diameter |

### What transfers and what doesn't

| Transfers directly | Does not transfer directly |
| --- | --- |
| Thrust-to-weight margins and motor-out / pack-out control authority (matched ratios) | Controller gains: a 1/3-scale vehicle responds about 1.7× faster (time scales with √length), so gains are retuned at full scale |
| Coaxial thrust loss as a ratio (measured on the thrust stand) | Absolute power, current, and noise |
| Software, mixer, failsafe logic, safety monitor logic, arming logic | Structural loads and vibration frequencies |
| Power architecture behavior: pack loss, switch faults, E-stop | Battery thermal behavior (different pack size) |
| Test procedures, checklists, log analysis | Parachute deployment dynamics |

The demonstrator is **heavier than a true scale model** (9.4 kg vs 6.9 kg for exact 1/3 scaling of 186 kg) because avionics, cameras, and the parachute don't shrink with the airframe. Thrust-to-weight, the ratio that matters most for control authority, is matched or exceeded instead.

---

## Configuration

| Parameter | Value |
| --- | --- |
| Layout | Coaxial X8: 4 folding arms, upper and lower counter-rotating rotor per arm |
| Propellers | T-Motor NS18x6 carbon, CW and CCW pairs |
| Rotor spacing | 1.1 × D = 503 mm between adjacent arm tips |
| Motor-to-motor diagonal | 711 mm (arm radius 356 mm) |
| Overall span | ~1.17 m |
| All-up weight | **~9.4 kg** with ballast, parachute, and full payload (~9.8 kg with 5% growth margin) |
| Bus | 6S (22.2 V nominal, 25.2 V full) |
| Estimated hover power | ~1,200 W (~14 A per pack) |
| Estimated hover endurance | **~15.5 min** to 20% charge |

---

## Parts

### Propulsion

| Part | Specs | Mass | Price |
| --- | --- | --- | --- |
| **T-Motor MN5008 KV340** | 6S with 18x6.1 prop: **4,215 g max thrust at 33.5 A (754 W)**; 1,238 g at 120 W; 1,541 g at 162 W; 35 A peak for 180 s ([T-Motor](https://store.tmotor.com/product/mn5008-kv340-motor-antigravity-type.html)) | 135 g | $89.99 |
| **T-Motor NS18x6 carbon props** | CW/CCW pair; thrust data above was measured with the P18x6.1, treated as equivalent (unv) ([T-Motor](https://store.tmotor.com/product/ns18x6-prop-uav-carbon-fiber.html)) | ~34 g per prop (unv) | $82.99 per pair |
| **Zubax Myxa A, 45 A** | DroneCAN and Cyphal, 1,200 W, 4–12S; covers the full 754 W peak ([Zubax](https://shop.zubax.com/products/zubax-myxa)) | 26 g | ~$165–200 (unv, EUR price) |

| ESC alternatives | Rating | Mass | Interface | Price | Trade |
| --- | --- | --- | --- | --- | --- |
| Holybro Kotleta20 ([Holybro](https://holybro.com/products/kotleta20)) | 500 W continuous | 8.8 g | DroneCAN | $57.99 | Saves ~$1,150, but needs a ~76% throttle cap on 18 in props: all-motor T/W drops to ~2.2 and pack-out to ~1.6 |
| ARK 4IN1 ([ARK](https://arkelectron.com/product/ark-4in1-esc/)) | 50 A per motor | 14.5 g | DShot with telemetry, no CAN | $218.50 | Breaks the CAN motor bus mirror |

Motor alternatives: MN5008 KV400 (3,982 g on 17 in, but 17 in breaks the 1.1 D scaling); MN5006 and MN4006 lack thrust for 2:1 at this weight.

### Power

| Part | Specs | Mass | Price |
| --- | --- | --- | --- |
| **4 × 6S1P Molicel P50B packs** | 5.0 Ah, 60 A continuous per cell; 108 Wh each, 432 Wh total; same cell as full scale ([Molicel](https://www.molicel.com/inr-21700-p50b/)) | ~450 g each built (unv) | ~$340 for 24 cells and pack build (unv) |
| **Pack switch board** (club-built, ×4) | Back-to-back MOSFETs, gate driver, soft-start acting as pre-charge, fuse; gate enable powered through the E-stop loop; safety monitor can also disable it | ~30 g each (unv) | ~$40 each (unv) |
| **Hardwired E-stop loop** | Series loop through a pull-pin loop key and a normally-closed E-stop powers every pack switch's gate enable. Opening the loop turns off all 4 packs with no software. A remote kill uses an independent receiver's PWM into a hardware comparator that breaks the same loop | in harness | in pack switch cost |
| **Holybro UBEC 12A** + **UBEC 5A** | 12 V for the Jetson, 5 V for the flight controller, fed from all 4 packs through ideal-diode ORing ([Holybro](https://holybro.com/collections/power-modules-pdbs)) | ~140 g with E-stop parts (unv) | $36.59 + $20.99 |

**Per-pack current limit:** two motors at full throttle draw ~67 A, above the P50B's 60 A continuous rating. Cap each pack at ~55 A in firmware (about 85–90% throttle). Per-pack voltage and current come from ESC telemetry; single-pack power modules like the Holybro PM03D don't fit a 4-pack design.

**Pack pairing:** each pack drives one rotor on an arm and the opposite-direction rotor on the diagonally opposite arm, so losing a pack stays balanced in yaw, roll, and pitch.

### Flight stack

| Part | Specs | Mass | Price |
| --- | --- | --- | --- |
| **Holybro Pixhawk 6X** + mini baseboard | STM32H753, triple IMU, Ethernet to the Jetson | 58 g | $286.98 with PM02D; **sold out** at time of search; 6C ($165.99) as a stopgap ([Holybro](https://holybro.com/products/pixhawk-6x)) |
| Holybro M10 GPS | GNSS and compass | 120 g listed, likely with mount | $43.99 ([Holybro](https://holybro.com/products/m10-gps)) |
| Holybro SiK Telemetry V3 915 MHz | Telemetry | 23.5 g | $58.99 ([Holybro](https://holybro.com/products/sik-telemetry-radio-v3)) |
| ExpressLRS receiver | RC link | ~5 g (unv) | ~$20 (unv) |
| Remote ID broadcast module | Must have an FAA-accepted Declaration of Compliance and a serial number. **The Holybro Remote ID module does not qualify for home-built drones** ([Holybro](https://holybro.com/products/remote-id)); Dronetag-class modules (unv) | ~30 g (unv) | ~$300 (unv) |
| ARK Flow | Optical flow and distance | ~10 g (unv) | $250 ([ARK](https://arkelectron.com/product/ark-flow/)) |
| iCEBreaker (safety monitor prototype) | iCE40UP5K | ~30 g (unv) | $79.95 |

### Recovery

| Part | Specs | Mass | Price |
| --- | --- | --- | --- |
| **Fruity Chutes Skycat Ultralight 22 lb (10 kg), 15 ft/s** | Pneumatic launcher; triggered by the FUSE servo-channel switch, which the flight controller or the FPGA safety monitor can drive ([Fruity Chutes](https://shop.fruitychutes.com/collections/skycat-launchers-2-5-kg-to-20-kg/products/skycat-bundle-ultra-light-22lbs-10kg-15fps)) | 469 g | $1,238.46; **sold out** at time of search |

The 10 kg rating leaves little margin above the 9.4 kg AUW. Watch mass growth; the next Skycat size up is the fallback.

### Perception payload (build stage 4)

Same parts as the [subscale perception BOM](avionics-parts.md#perception-boms): Jetson Orin Nano Super, 2 × OV9281, OAK-D Pro W, LightWare SF45/B, SIYI HM30. About $2,700. Flown only after the airframe is proven; a dummy mass of the same weight and position replaces it for early flights.

---

## Airframe

**Build, don't buy.** No commercial X8 frame found matches the 711 mm, 1.1 D layout (Tarot X8-class frames are ~960 mm wheelbase, unv), and a club-built frame lets Mechanical practice the full-scale design decisions at low cost.

| Element | Design |
| --- | --- |
| Centre structure | Two 3 mm carbon plates standing in for the 4130 cage; 4 pack bays in the same positions as full scale |
| Arms | 4 × 25 mm OD carbon tubes, ~300 mm, at the full-scale arm angles |
| Fold hinge and lock | Locking folding clamps as a scaled full-scale hinge, with an arm-lock sensor wired to the flight controller |
| Coaxial motor mounts | Upper motor on top, lower motor inverted underneath, gap matched to full scale in prop diameters |
| Ballast | 2.7 kg on a rail at the seat position, adjustable fore-aft and side to side |
| Landing gear | Scaled skids |
| Parachute mount | Same location as full scale |
| Estimate | ~1,150 g frame and gear (unv), ~$450 in parts (unv) |

---

## Mass budget

| Item | Qty | Unit g | Total g |
| --- | ---: | ---: | ---: |
| MN5008 KV340 motors | 8 | 135 | 1,080 |
| NS18x6 props (unv) | 8 | 34 | 272 |
| Myxa A ESCs | 8 | 26 | 208 |
| Power and CAN harness (unv) | 1 | 300 | 300 |
| Frame: plates, arms, folding locks, coaxial mounts, gear (unv) | 1 | 1,150 | 1,150 |
| 6S1P P50B packs (unv) | 4 | 450 | 1,800 |
| Pack switch boards (unv) | 4 | 30 | 120 |
| E-stop loop, BECs, diode-OR (unv) | 1 | 140 | 140 |
| Pixhawk 6X and mini baseboard | 1 | 58 | 58 |
| M10 GPS (listed) | 1 | 120 | 120 |
| SiK telemetry and ELRS receiver | 1 | 29 | 29 |
| ARK Flow (unv) | 1 | 10 | 10 |
| Jetson Orin Nano Super and 2 × OV9281 (cameras unv) | 1 | 205 | 205 |
| OAK-D Pro W | 1 | 91 | 91 |
| LightWare SF45/B | 1 | 59 | 59 |
| SIYI HM30 air unit | 1 | 110 | 110 |
| iCEBreaker safety monitor (unv) | 1 | 30 | 30 |
| Club sensor boards (allowance) | 1 | 150 | 150 |
| Payload tray and vibration isolation (unv) | 1 | 120 | 120 |
| Remote ID module (unv) | 1 | 30 | 30 |
| Skycat 10 kg parachute | 1 | 469 | 469 |
| **Pilot ballast** 2,700 g plus adjustable rail | 1 | 2,820 | 2,820 |
| **All-up weight** | | | **9,371** |
| With 5% growth margin | | | ~9,840 |

---

## Thrust and endurance

Coaxial factor 0.85 on every rotor, as in the full-scale baseline. 4,215 g maximum thrust per rotor.

| Case | Thrust | T/W at 9.37 kg | Packs capped at 55 A | Near 20% charge (thrust × 0.77, est) |
| --- | ---: | ---: | ---: | ---: |
| All 8 rotors | 28.7 kgf | **3.06** | 2.60 | 2.36 |
| One motor out | 25.1 kgf | **2.68** | — | 2.06 |
| One pack out (6 rotors) | 21.5 kgf | **2.29** | 1.95 | 1.77 |

Full scale for comparison: 2.19 / 1.92 / 1.65. **The demonstrator exceeds full-scale margins in every case with fresh packs.** Near the end of a discharge the pack-out case drops below 2:1, so motor-out and pack-out tests run with charge margin, and the low-battery failsafe lands before the packs sag that far. If mass passes ~10 kg, the fallback is MN5212 or MN501-S class motors (unv).

**Hover:** 9,371 g ÷ 8 × 1.18 coaxial factor ≈ 1,382 g per rotor (~47% throttle) → ~140 W per motor from the MN5008 table. 8 × 140 W plus 4% ESC loss ≈ 1,165 W, plus ~40 W avionics → **~1,205 W**, about 14 A per pack.

**Endurance:** 432 Wh × 80% usable = 346 Wh; with a 10% derate, 311 Wh → **~15.5 min** hover (~14.5 min at 9.84 kg).

**One pack out:** 6 rotors at ~1,843 g and ~208 W each, ~20 A per remaining pack. Enough to land.

---

## Build stages

| Stage | Configuration | Exit criteria |
| --- | --- | --- |
| **0. Simulation** | PX4 or ArduPilot software-in-the-loop, coaxial X8 model with this mass and thrust | Mixer, failsafes, motor-out and pack-out cases behave correctly in simulation |
| **1. Bench** | Thrust stand: single MN5008, then a coaxial pair; pack switch boards; E-stop loop | Coaxial factor measured; switch, pre-charge, and E-stop tests pass; pack logging works |
| **2. Airframe flying** | Full airframe, flight stack, 4-pack power, parachute, **dummy mass instead of the perception payload** | Stable tethered then free hover; tuned; motor-out and pack-out response demonstrated |
| **3. Safety systems proven** | iCEBreaker safety monitor active; parachute trigger tested | Monitor trips correctly on heartbeat loss and limit exceedance (on tether); parachute deployment verified |
| **4. Perception installed** | Jetson, cameras, OAK-D, lidar, video link replace the dummy mass | VIO and datasets logged against flight controller estimates; obstacle warnings working |
| **5. Club boards** | Club-built sensor nodes, flight controller, safety monitor board swapped in one at a time | Each board passes bring-up, then logs flight time alongside or instead of the commercial part |

---

## Test plan

Every powered test runs under a written procedure and a Test Readiness Review signed by the Chief Safety Officer (Article X, Section 4). These are the same test types planned for the full-scale vehicle.

| Test | What it proves | Full-scale counterpart |
| --- | --- | --- |
| Single-rotor thrust stand | Motor, prop, ESC data vs catalog | X13 G2 characterization |
| Coaxial pair on thrust stand | Actual coaxial thrust and power factor vs the 0.85 / +18% assumption | Coaxial spacing study |
| Pack switch and pre-charge bench test | Switching, inrush, fault response | Contactor and pre-charge qualification |
| E-stop loop test | All packs open with no software | Hardwired E-stop verification |
| Pack-out on the thrust stand | Remaining packs and ESCs carry the load | Pack-out requirement |
| Restrained and tethered hover | Control loop stability, vibration, logging | Phase 2 tether campaign |
| Free hover and tuning | Attitude and position control, estimator health | Full-scale tuning |
| **Motor-out in flight** | Controlled hover and landing with one motor disabled | Article X, Section 5(d) motor-out demonstration |
| **Pack-out in flight** | Controlled landing with one pack switched off (two opposite rotors) | Pack-out requirement |
| Safety monitor trip (on tether) | Heartbeat loss and limit exceedance cut power as designed | Safety monitor qualification |
| Parachute deployment | Trigger and deployment at a safe height | Recovery system verification |
| Endurance and thermal | Pack, ESC, and motor temperatures; flight time | Pack thermal test |
| Perception flights | VIO and mapping accuracy vs GNSS and flight controller | Companion computer validation |

**Test site:** open, off-campus, with written site permission, an exclusion zone, a safety observer, and fire suppression for lithium packs (Article X, Sections 6 and 7).

**Thrust stand:** Tyto Robotics Series 1585 (5 kgf, 55 A, by quote; [Tyto](https://www.tytorobotics.com/pages/series-1580-1585)) covers one rotor. A coaxial pair (~7 kgf) needs a Tyto Flight Stand quote or two 1585s on a rigid fixture (unv).

---

## Regulatory and University checklist

- [ ] **Operating rules.** [49 USC 44809](https://www.law.cornell.edu/uscode/text/49/44809) covers recreational flying, and its note extends to educational or research use at institutions of higher education; how the FAA applies that to a student club building a development article is unclear (unv). **Plan to fly under 14 CFR Part 107** with a certificated remote pilot in command.
- [ ] **Remote pilot certificate** for every pilot in command (FAA knowledge test).
- [ ] **Registration** on FAADroneZone (over 250 g, under 55 lb; the demonstrator is ~21 lb); number marked on the outside.
- [ ] **Remote ID** ([14 CFR 89](https://www.ecfr.gov/current/title-14/part-89)): a broadcast module with an FAA-accepted Declaration of Compliance and its serial number on the registration, or fly only inside an FAA-recognized identification area.
- [ ] **Airspace:** LAANC or other authorization in controlled airspace (central Austin is near Austin-Bergstrom Class C, unv); visual line of sight; at or below 400 ft AGL.
- [ ] **UT Austin:** no drone policy was found in the Handbook of Operating Procedures (unv). Ask the University Policy Office (policyoffice@austin.utexas.edu, 512-471-0426) and Risk Management which approvals and insurance apply to a student organization, and get written approval from the University Advisor for each site.
- [ ] **Texas law:** review Texas Government Code chapter 423 on drone imaging and restricted areas before flying cameras (unv).
- [ ] **Lithium batteries:** charging and storage approved by UT Environmental Health and Safety (Article X, Section 7).

---

## Cost

| Item | Cost |
| --- | ---: |
| MN5008 KV340 × 8 | $720 |
| NS18x6 props × 6 pairs (2 spare pairs) | $498 |
| Zubax Myxa A 45 A × 8 (unv, EUR price) | ~$1,600 |
| Frame parts (unv) | $450 |
| P50B cells and pack build, 4 packs (unv) | $340 |
| Charger (unv) | $150 |
| Pack switch boards and E-stop (unv) | $200 |
| UBECs and diode-OR (unv) | $100 |
| Pixhawk 6X, mini baseboard, PM02D | $287 |
| M10 GPS, SiK telemetry, ELRS receiver | $123 |
| Skycat 10 kg parachute | $1,238 |
| Remote ID module with FAA Declaration of Compliance (unv) | $300 |
| Ballast and rail (unv) | $60 |
| ARK Flow | $250 |
| iCEBreaker | $80 |
| **Airframe, power, flight stack, safety subtotal** | **≈ $6,400** |
| Thrust stand (Tyto 1585, by quote, est) | ≈ $1,600 |
| Perception payload ([subscale BOM](avionics-parts.md#perception-boms)) | ≈ $2,700 |
| **Phase 1 total** | **≈ $10,700** |

- **Flying, safety-complete demonstrator including the thrust stand: ≈ $8,000.**
- Kotleta20 ESCs instead of Myxa save ~$1,150 at the cost of the throttle cap described above.
- The perception payload is stage 4 and can be funded separately.

---

## Open items

1. Confirm Myxa pricing and lead time, or accept the Kotleta20 throttle cap.
2. Confirm Skycat stock; watch mass growth against its 10 kg rating.
3. Design the pack switch board (MOSFETs, gate driver, soft-start pre-charge, current sense) as the club's first power board.
4. Choose a Remote ID module with an FAA-accepted Declaration of Compliance.
5. Confirm Pixhawk 6X stock or buy the 6C as a stopgap.
6. Confirm UT approvals and a test site.
7. Get a Tyto Robotics quote, including a coaxial-pair setup.
8. Confirm NS18x6 prop mass and that its thrust matches the P18x6.1 test data.

## Sources

- Motors: [MN5008 KV340](https://store.tmotor.com/product/mn5008-kv340-motor-antigravity-type.html), [MN5008 KV400](https://store.tmotor.com/product/mn5008-kv400-motor-antigravity-type.html), [MN5006](https://store.tmotor.com/product/mn5006-kv300-motor-antigravity-type.html), [MN4006](https://store.tmotor.com/product/mn4006-kv380-motor-antigravity-type.html)
- Props: [NS18x6](https://store.tmotor.com/product/ns18x6-prop-uav-carbon-fiber.html)
- ESCs: [Zubax Myxa](https://shop.zubax.com/products/zubax-myxa), [Holybro Kotleta20](https://holybro.com/products/kotleta20), [ARK 4IN1](https://arkelectron.com/product/ark-4in1-esc/)
- Flight stack and power: [Pixhawk 6X](https://holybro.com/products/pixhawk-6x), [Pixhawk 6C](https://holybro.com/products/pixhawk-6c), [M10 GPS](https://holybro.com/products/m10-gps), [SiK V3](https://holybro.com/products/sik-telemetry-radio-v3), [Holybro power modules and BECs](https://holybro.com/collections/power-modules-pdbs), [Holybro Remote ID](https://holybro.com/products/remote-id)
- Battery: [Molicel P50B](https://www.molicel.com/inr-21700-p50b/)
- Recovery and test: [Fruity Chutes Skycat 10 kg](https://shop.fruitychutes.com/collections/skycat-launchers-2-5-kg-to-20-kg/products/skycat-bundle-ultra-light-22lbs-10kg-15fps), [Tyto Robotics Series 1580/1585](https://www.tytorobotics.com/pages/series-1580-1585)
- Regulatory: [49 USC 44809](https://www.law.cornell.edu/uscode/text/49/44809), [14 CFR Part 89](https://www.ecfr.gov/current/title-14/part-89), [UT University Policy Office](https://compliance.utexas.edu/university-policy-office/)
