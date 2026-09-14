# Longhorn eVTOL

A student organization at The University of Texas at Austin that designs, builds, and tests an electric vertical takeoff and landing (eVTOL) aircraft.

Our goal is a **single-occupant, seated multirotor** ("the Vehicle") capable of safe, low-altitude piloted flight.

**Website:** [longhorn-evtol.vercel.app](https://longhorn-evtol.vercel.app) · **Site source:** [website](https://github.com/LonghornEVTOL/website)

> **Status:** Founding. Constitution drafted; registration with the Dean of Students is pending a University Advisor.

---

## Current phase: 1/3-scale demonstrator

We are building an unmanned, 1/3-scale copy of the full-scale vehicle that mirrors its architecture, so software, safety systems, and test procedures are proven before anything full size flies. **Full design: [`docs/vehicle/subscale-demonstrator.md`](docs/vehicle/subscale-demonstrator.md).**

| | 1/3-scale demonstrator |
| --- | --- |
| Layout | Coaxial X8, 4 folding arms, ~1.17 m span, 18 in props at full-scale rotor spacing |
| Propulsion | 8 × T-Motor MN5008 KV340 with Zubax Myxa DroneCAN ESCs |
| Power | 4 independent 6S Molicel P50B packs (same cell as full scale), per-pack switches, hardwired E-stop |
| Pilot stand-in | 2.7 kg adjustable ballast (160 lb pilot scaled) |
| Weight and thrust | ≈ 9.4 kg; thrust-to-weight 3.06, 2.68 with a motor out, 2.29 with a pack out |
| Flight time | ≈ 15.5 min hover |
| Safety | Independent FPGA safety monitor, Fruity Chutes Skycat parachute, written test procedures |
| Cost | ≈ $8,000 to a flying, safety-complete demonstrator including a thrust stand; ≈ $10,700 with the perception payload |

Build order: simulation → thrust stand and power bench tests → airframe flying with dummy payload → safety systems proven → cameras and perception → club-built boards swapped in one at a time.

Flight approvals (FAA Part 107, Remote ID, UT HOP 8-1070, test sites): [`docs/regulatory/subscale-flight-approvals.md`](docs/regulatory/subscale-flight-approvals.md). First club power board, the per-pack switch: [`docs/hardware/pack-switch-board.md`](docs/hardware/pack-switch-board.md).

---

## Mission and approach

Development is phased. Each phase is gated on a formal design and safety review, and nothing advances until the review passes.

| Phase | Test article | Gate to advance |
| --- | --- | --- |
| 1 | Subscale unmanned demonstrators | Design and safety review |
| 2 | Full-scale unmanned and tethered test article | Design and safety review, documented run time |
| 3 | Piloted vehicle | All conditions precedent met (see below) |

### Regulatory basis

Piloted flight is intended to fall under **14 CFR Part 103 (Ultralight Vehicles)**:

- Single occupant
- Under **254 lb** empty weight
- Under **55 knots** full-power level speed
- Daylight, visual flight rules only
- Not over congested areas

Part 103 requires no airworthiness certificate and no pilot license. **The 254 lb empty weight limit is the binding design constraint** and drives the architecture from day one.

We will not fly on campus. Testing happens at an off-campus site, and the piloted phase requires named faculty oversight.

---

## Vehicle baseline

Sized for a **160 lb pilot** with total thrust at **twice the gross weight** (vehicle, batteries, and pilot). Frozen at the Preliminary Design Review. Parts, sources, both propulsion options, the custom ESC design, and cost: [`docs/vehicle/baseline.md`](docs/vehicle/baseline.md). Alternative layouts we could pivot to (flat octocopter, coaxial X12, flat hexacopter), sized on the same assumptions: [`docs/vehicle/configuration-trade-study.md`](docs/vehicle/configuration-trade-study.md).

| | Baseline v1 (preliminary) |
| --- | --- |
| Configuration | Coaxial octocopter (X8): 4 folding arms, 8 counter-rotating rotors |
| Propulsion | 8 × Hobbywing X13 G2 integrated units (motor, ESC, 56 in folding prop), 4.19 kg and 60 kgf max each |
| Thrust | 408 kgf total after coaxial losses, against 372 kgf needed for 2:1 |
| Thrust-to-weight | 2.19 all motors · 1.92 one motor out · 1.65 one pack out |
| Battery | 4 independent 18S5P packs of Molicel P50B 21700 cells, 64.8 V nominal, 6.5 kWh total, ≈ 9.6 kg each |
| Hover power | ≈ 24 to 27 kW; ≈ 8 min planned flight (≈ 12 min to 20% charge by energy, pending thermal test) |
| HV protection | Per pack: Gigavac GX14 contactor, pre-charge, EV fuse, ADBMS1818 BMS board; hardwired E-stop opens all packs |
| Low-voltage power | Redundant Vicor DCM3623 converters, or a team-built LTC7801 buck board |
| Flight controller | Pixhawk 6X Pro (triple IMU, STM32H753, real-time OS), dual Here4 GNSS, LW20/C LiDAR |
| Safety monitor | Microchip IGLOO2 M2GL010 FPGA and Murata SCH16T IMU on their own power and reset (prototype on iCEBreaker), can cut power and fire the parachute |
| Companion | NVIDIA Jetson Orin NX 16GB on a club carrier, advisory only |
| Recovery | Galaxy GRS 3 270 ballistic parachute, 9.5 kg |
| Subscale demonstrator | ≈ 1/3 scale coaxial X8, 15 in props, 6S LiPo, ≈ 5 kg |

**Mass budget**

| Item | kg |
| --- | ---: |
| Airframe (4130 centre cage, folding carbon arms) | 14.5 |
| Landing gear and motor mounts | 5.0 |
| Propulsion units (8) | 33.5 |
| Battery packs (4) with protection | 38.6 |
| HV distribution, avionics, LV power | 5.0 |
| Seat, restraint, controls | 7.3 |
| Ballistic parachute | 9.5 |
| **Empty weight** | **113.3 kg (250 lb)** with parachute · **103.8 kg (229 lb)** if the parachute is excluded under 103.1(e)(1) |
| Pilot | 72.6 kg (160 lb) |
| **Gross** | **185.9 kg (410 lb)** |

**Open risks.** Margin to 254 lb is only 4 lb unless the FAA confirms the parachute exclusion. The integrated ESC's rating during a motor-out landing must be verified on the thrust stand. Hardware cost is ≈ $20,000 to 22,000.

---

## Safety

A student organization proposing to put a person on a multirotor should expect more scrutiny than a normal club. Our constitution (Article X) is built for that.

- **Separate safety authority.** The VP of Engineering owns the design. The VP of Safety and Operations (Chief Safety Officer) owns stop authority. One person may never hold both roles. If the safety role is vacant, all fabrication and test activity is suspended.
- **Stop authority cannot be overridden.** The Chief Safety Officer can halt any operation. The President, the Executive Board, and a vote of the membership cannot override it.
- **Custom hardware earns its way in.** Custom PCBs and programmable logic in a flight-critical path require a design review, current-limited bring-up, and qualification at or above expected operating conditions. Nothing custom flies piloted until it has documented run time on the unmanned article.
- **Conditions precedent to piloted flight.** Nine conditions must be met before any piloted flight, including an established FAA basis, verified margins, demonstrated motor-out response, a functional E-stop and recovery system, insurance, and written sign-off from four people. No vote can waive any of them.
- **Flight-critical compute architecture.**
  - Stabilization runs on a dedicated real-time controller, never on a device running a general-purpose OS.
  - The safety monitor lives on physically separate hardware with its own power and reset domain.
  - Companion computing is advisory only.
  - Performance claims must be measured end-to-end and backed by test data.

---

## Team structure

| Team | Sub-teams |
| --- | --- |
| **Manufacturing and Operations** | Welding and metal fab · Composites and layup · Machining and CNC (Texas Inventionworks) · Assembly and QC |
| **Mechanical Design** | Airframe and chassis · Seating, ergonomics, and cockpit · Propulsion and duct · Landing gear and suspension |
| **Electrical and Power** | PDU and wiring harness · ESC and motor integration · Avionics and power architecture · Safety interlocks and E-stop · Custom PCB and hardware design |
| **Software and Avionics** | Flight control (GNC) · Sensor fusion and telemetry · Cockpit interface and ground station · Fault management and failsafes · Hardware acceleration and FPGA |
| **Flight Test and Range Operations** | Test planning · Range and logistics · Data and instrumentation |
| **Systems Engineering and Integration** | Requirements and interfaces · Configuration management |
| **Business, Outreach, and Sponsorship** | Sponsorship · Recruitment and outreach · Media and documentation |

### FPGA scope

FPGA work is deliberately narrow. Three workstreams only:

1. Independent safety monitor
2. Companion computing
3. Hardware-in-the-loop (HIL) rig

Sensor fusion acceleration and motor command generation are out of scope. A microcontroller handles those, and control-loop latency is dominated by IMU group delay, ESC update rate, and propeller inertia, not mixer compute.

ASICs are a possible future direction, not a commitment. They may never sit in the stabilization, motor command, safety monitor, or recovery path of a piloted vehicle.

---

## Projects

Highlights by team, sized from a weekend to multiple semesters. The **complete list** of everything the vehicle needs (every subsystem, custom PCB, software module, and test rig, with build-or-buy and owners) is in [`docs/vehicle/subsystems.md`](docs/vehicle/subsystems.md).

**Electrical and Power**

| Project | What it involves |
| --- | --- |
| Buck converter board | Design, lay out, and bring up a 6S (25 V) to 5 V, 3 A converter that powers the subscale avionics |
| Current and voltage sense board | Shunt plus current-monitor IC board that logs motor current on the thrust stand |
| Subscale power distribution board | High-current PDB with fusing and a voltage tap for the 6S demonstrator |
| Battery management system | Per-pack firmware on the BMS board: state of charge, cell balancing, temperature limits, fault reporting to the flight controller over CAN, and automatic pack logging for Article X |
| Power distribution unit | Full-scale bus bars, fuses, contactors, and per-branch current sensing that route pack power to the ESCs |
| Vehicle wiring harness | HV, low-voltage, and CAN wiring between packs, ESCs, flight controller, safety monitor, and cockpit: wire gauges, connectors, routing through folding arms, and harness drawings |
| Pre-charge and contactor driver | HV bus pre-charge, contactor coil driver, and interlock for the full-scale packs |
| Hardwired E-stop loop | Contactor interlock chain that opens every pack with no software in the path |
| 18S BMS board | Per-pack cell voltage and temperature measurement hardware on an ADI ADBMS1818 with isoSPI |
| Low-voltage buck board | 75 V to 12 V avionics supply on an ADI LTC7801, as a redundant alternative to COTS modules |
| Custom ESC | 18S, 150 A FOC inverter on Infineon 150 V OptiMOS FETs, a 6ED2742S01Q gate driver, ACS772 current sensing, and VESC firmware. Dyno and subscale first, then the unmanned article only. |

**Avionics PCBs**

Designed in KiCad and hand-assembled by the club ([PCB design guide](docs/hardware/pcb-design-guide.md)). Exact chips, specs, prices, assembly difficulty, and a funding total are in [`docs/vehicle/avionics-parts.md`](docs/vehicle/avionics-parts.md). Custom boards fly first on the subscale drone and HIL rig, then the unmanned article. Commercial parts (Pixhawk 6X Pro, Here4 GNSS) stay on the piloted vehicle until a custom board passes Article X qualification.

| Project | What it involves |
| --- | --- |
| Flight controller board | STM32H753 running PX4 or ArduPilot; ICM-45686, BMI088, and IIM-42653 IMUs; BMP390 and MS5611 barometers; MMC5983MA magnetometer; dual CAN FD, FRAM and microSD logging, redundant power |
| GNSS and compass node | u-blox ZED-F9P-04B plus IIS2MDC or RM3100 magnetometer on a DroneCAN node |
| Barometer node | Two Bosch BMP581 barometers in a shielded, vented enclosure on DroneCAN |
| Optical flow node | PixArt PAA3905 flow sensor and Broadcom AFBR-S50LV85D distance sensor on DroneCAN |
| IMU breakout and vibration logger | Measure frame vibration at candidate mounting spots |
| Power monitor node | Isolated per-pack voltage and current on DroneCAN |
| Pilot controls interface | Hall-effect stick and throttle inputs with redundant ADCs to CAN |
| Cockpit display board | Battery, time remaining, altitude, and warnings for the pilot |
| Safety monitor board | Flight unit on an IGLOO2 M2GL010 FPGA with a Murata SCH16T IMU, isolated power, window watchdog, contactor and parachute drivers |
| Companion carrier board | Carrier for the Jetson Orin NX with MIPI camera inputs, Ethernet, and CAN |
| LV distribution board | 12 V and 5 V rails with eFuses and per-load monitoring |
| CAN bus tools | USB-to-CAN adapter, termination, and breakout boards for bench work |

**Software and Avionics**

| Project | What it involves |
| --- | --- |
| Thrust stand data logger | Python tool that records load cell, RPM, current, and voltage and plots thrust and efficiency curves |
| Ground station dashboard | Live MAVLink telemetry display for the subscale demonstrator |
| Single-axis PID rig | Tune attitude control on a one-degree-of-freedom test bench |
| Coaxial X8 simulation | Software-in-the-loop model of the vehicle, including motor-out and pack-out cases |
| Failsafe logic | Geofence, loss of link, and motor-out compensation on the flight controller |
| State estimation tuning | EKF fusing IMU, barometer, GNSS, magnetometer, LiDAR, and optical flow |
| Pilot control mapping and envelope protection | Fly-by-wire stick mapping, altitude hold, and tilt, descent rate, and altitude limits |
| DroneCAN node firmware | Shared firmware base for every custom CAN board |
| Pilot training simulator | Flight simulator using the real stick and display, driven by the vehicle model |

**Perception and Cameras**

Runs on the companion computer. Advisory only on the piloted vehicle: it informs the pilot, ground crew, and logs, with no path to motor command.

| Project | What it involves |
| --- | --- |
| Camera integration | Global-shutter downward, forward, and cockpit cameras, hardware-synced to the IMU |
| Optical flow | Velocity over ground from the downward camera and flow node |
| Visual-inertial odometry | Position and velocity from camera plus IMU (OpenVINS or VINS-Fusion) |
| SLAM and mapping | Map of the test site for position logging and site surveys (ORB-SLAM3 class) |
| Obstacle detection | Scanning LiDAR or depth camera warnings to the pilot |
| Video downlink | Live forward and pilot camera feeds to the ground station |
| FPGA image preprocessing | Camera capture and feature detection on an AMD Kria KV260 kit |
| Dataset capture | Synchronized camera, IMU, and GNSS logs from subscale flights |

**Hardware Acceleration and FPGA**

| Project | What it involves |
| --- | --- |
| Heartbeat watchdog | First RTL on a small FPGA dev board: watch a heartbeat signal and trip an output when it stops |
| Safety monitor RTL | Heartbeat, attitude, rate, and power limit checks with authority to cut power, plus testbenches |
| Hardware-in-the-loop rig | Run flight software and the safety monitor against a simulated vehicle |
| FPGA image preprocessing | See Perception and Cameras (companion computing workstream) |

**Mechanical Design and Manufacturing**

| Project | What it involves |
| --- | --- |
| Motor mount bracket | CAD, hand calcs, FEA, then machine it at Texas Inventionworks |
| Thrust stand frame | Rigid stand with a load cell rated past 80 kgf for single and coaxial motor testing |
| Subscale airframe | Design and build the 1/3 scale coaxial X8 frame |
| Composite arm coupons | Lay up carbon tube samples and test them to failure to set arm design allowables |
| Landing skid drop test | Size skids for hard landing loads and verify with a drop rig |
| Weld coupons | Practice and test tube joints before any airframe welding |
| Folding arm joint and lock | Hinge that locks rigid in flight, with a lock sensor the flight controller checks before arming |
| Battery bays | Four bays with fire barriers, retention, cooling air paths, and quick removal |
| Parachute mount | Hard points and load path sized for deployment shock |
| Seat, restraint, and pilot controls | Carbon seat, harness mounts sized for crash loads, and stick placement |

**Flight Test, Systems, and Outreach**

| Project | What it involves |
| --- | --- |
| Motor and propeller characterization | Written test procedure, then thrust stand runs that replace the catalog numbers in the baseline |
| Motor-out thermal test | Hold one X13 G2 at 40 to 45 kgf for 120 s and log ESC and motor temperature. This is the first open risk in the baseline. |
| Cell pulse resistance test | Measure 10 s pulse resistance of P50B, P60B, and 40PL cells to choose the pack cell |
| Mass budget tracker | Keep the 254 lb budget current as parts are weighed and selected |
| Interface control documents | Define power, data, and mechanical interfaces between teams |
| Test site survey | Find and document off-campus sites for subscale and tethered testing |
| Tether test rig | Tether, load cell, anchor, and quick release for Phase 2 |
| Hazard analysis | Functional hazard assessment and FMEA, kept current as the design changes |
| Configuration trade study | Coaxial X8 vs flat octo vs coaxial X12, settled by thrust stand and subscale tests |
| Sponsor packet | One-page and deck versions of the program for sponsors |

---

## Membership

- Open to UT Austin students, faculty, and staff by **application and interview**, recruited every fall and spring. **No prior experience, specific major, or fee required.** Apply at [longhorn-evtol.vercel.app/apply](https://longhorn-evtol.vercel.app/apply).
- Applicants are scored on three published criteria (interest, commitment, contribution) with the same rubric, and every applicant gets a written decision within 14 days after interviews close. See the [interview guide](docs/recruitment/interview-guide.md).
- **General Members** are admitted applicants. Info sessions and outreach events stay open to everyone.
- **Active Members** complete safety onboarding and attend three meetings or work sessions. They get team assignments, shop access, and a vote.
- General meetings are held twice a month, and each team meets weekly.
- Every vehicle configuration goes through a Preliminary Design Review, a Critical Design Review, and a Test Readiness Review before powered testing.

---

## Leadership

| Role | Name |
| --- | --- |
| President | Pranav Shivashankar |
| VP of Engineering (Chief Engineer) | Rihan Babu |
| VP of Safety and Operations (Chief Safety Officer) | Akshay Mallireddy |

Treasurer, Secretary, and Director of Outreach are open positions.

## Funding

- **No dues.** Funding comes from sponsorship, grants, and University sources.
- No member is ever required to pay for anything or buy materials out of pocket. Anyone who fronts money for an approved purchase is reimbursed.
- Spending over set thresholds requires multiple officer approvals, and flight-critical hardware purchases also require Chief Safety Officer concurrence.

Interested in sponsoring? Reach out to the President.

---

## Repository layout

```
docs/              Vehicle baseline, parts, PCB design guide, recruitment, constitution, reviews, safety
hardware-lib/      Shared KiCad symbols, footprints, and 3D models
manufacturing/     Welding and fab, composites, machining and CNC, assembly and QC
mechanical/        Airframe, cockpit and ergonomics, propulsion and duct, landing gear
electrical/        PDU and harness, ESC and motors, power architecture, safety interlocks, power PCBs
avionics-pcb/      Flight controller, sensor nodes, safety monitor board, companion carrier, cockpit boards
software/          Flight control, perception and cameras, DroneCAN firmware, ground station, simulator
fpga/              Safety monitor, companion computing, HIL rig
flight-test/       Test plans, range logistics, flight data
systems/           Requirements, interfaces, configuration management
outreach/          Sponsorship, recruitment, media
```

Most folders are empty placeholders and will fill in as each team starts work.
