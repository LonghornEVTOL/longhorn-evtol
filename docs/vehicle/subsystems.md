# Vehicle Subsystems and Work Breakdown

**Status:** Preliminary, tied to [Baseline v1](baseline.md) (coaxial X8). This is the full list of what the vehicle and its test program need: every subsystem, what it contains, whether we buy it or build it, the custom PCBs, the software, and which team owns it. If something the vehicle needs is not on this list, add it.

Last updated: 14 September 2026

---

## How to read this

**Build vs buy.** Article X, Section 3 decides this for anything flight-critical:

| Tag | Meaning |
| --- | --- |
| **Buy** | Proven commercial part flies on the piloted vehicle. |
| **Build** | Team-designed. Not flight-critical, or used only on test rigs. |
| **Buy → Build** | Commercial part flies first. The team builds its own version and runs it on the thrust stand, HIL rig, subscale drone, and unmanned article. It may replace the commercial part only after design review, current-limited bring-up, qualification, and documented run time on the unmanned article. |

**Scope** is a rough size for a project: **S** is a few weeks for a small group, **M** is about a semester, **L** is multiple semesters.

**Owner** abbreviations: MECH Mechanical Design, MFG Manufacturing and Operations, ELEC Electrical and Power, SW Software and Avionics, FT Flight Test and Range, SYS Systems Engineering, BIZ Business and Outreach.

---

## System architecture

```
                         ┌────────────────────── Ground ──────────────────────┐
                         │ Ground station · telemetry · video · RSO E-stop    │
                         └───────────▲──────────────▲───────────────▲─────────┘
                                     │ 900 MHz       │ RC link       │ independent E-stop radio
┌──────────────────────────── Vehicle ────────────────────────────────────────────────────────┐
│                                                                                              │
│  Pilot controls ──CAN──▶ ┌───────────────────┐ ◀──CAN── GNSS + mag node (×2)                 │
│  Cockpit display ◀─CAN── │ Flight controller │ ◀──CAN── Barometer node, LiDAR, optical flow  │
│                          │ (real-time only)  │ ──CAN──▶ 8 × ESC (DroneCAN, telemetry back)   │
│                          └───────┬───────────┘                                               │
│                         heartbeat│ attitude/rate          ┌──────────────────────────┐       │
│                                  ▼                        │ Companion (Kria K26)     │       │
│                          ┌───────────────────┐            │ cameras · VIO/SLAM ·     │       │
│  Independent IMU ──────▶ │ Safety monitor    │            │ logging · video          │       │
│  E-stop (pilot, RSO) ──▶ │ (FPGA, own power) │            │ ADVISORY ONLY: no path   │       │
│                          └──┬─────────────┬──┘            │ to motor command         │       │
│                     open    │             │ fire          └──────────────────────────┘       │
│                  contactors ▼             ▼                                                  │
│  4 × battery pack ──▶ PDU (contactors, pre-charge, fuses) ──▶ 8 × ESC/motor   Parachute      │
│       │ BMS ──CAN──▶ flight controller                                                       │
│       └──▶ redundant DC-DC ──▶ LV distribution (12 V / 5 V) ──▶ avionics, cockpit, cameras   │
└──────────────────────────────────────────────────────────────────────────────────────────────┘
```

Two CAN buses (primary and backup) carry flight-critical traffic. Companion computing gets telemetry but never sits between the flight controller and the motors (Article X, Section 10).

---

## 1. Airframe and structures

| Item | What it involves | Build/buy | Scope | Owner |
| --- | --- | --- | --- | --- |
| Centre cage | 4130 chromoly welded tube frame carrying seat, packs, parachute, avionics | Build | L | MECH, MFG |
| Folding arms | 4 carbon fiber tubes with fold hinges | Build | L | MECH, MFG |
| Arm lock and lock sensor | Positive lock in flight position plus a switch the flight controller checks before arming | Build | M | MECH, ELEC |
| Coaxial motor mounts | Upper and lower motor mounting at each arm tip, tuned for vibration | Build | M | MECH, MFG |
| Landing gear | Skids sized for hard landings, drop tested | Build | M | MECH |
| Battery bays | 4 bays with fire barriers, retention, cooling air paths, quick removal | Build | M | MECH, ELEC |
| Avionics bay | Vibration-isolated mounting for flight controller and safety monitor | Build | S | MECH |
| Parachute mount | Hard points and a load path sized for deployment shock | Build | M | MECH |
| Fairings and panels | Composite covers, cockpit panel | Build | M | MFG |
| Transport cradle | Trailer fixture and ground handling points | Build | S | MFG |
| Rotor guards (trade) | Guards or ducts, per the configuration trade study | Build | M | MECH |

**Analyses required:**
- FEA load cases: 2 g maneuvering, hard landing, motor out, parachute deployment, and arm lock
- Modal analysis to keep frame resonances away from rotor speeds
- Fatigue on arm joints
- Occupant crash protection
- Mass properties and center of gravity tracking

---

## 2. Propulsion

| Item | What it involves | Build/buy | Scope | Owner |
| --- | --- | --- | --- | --- |
| Propulsion units | 8 × Hobbywing X13 G2 plus 2 spares | Buy | S | ELEC |
| Propeller balancing | Static and dynamic balancing procedure | Build | S | MFG |
| Coaxial spacing study | Upper-to-lower rotor gap vs efficiency, on the thrust stand | Build | M | MECH, FT |
| Motor thermal monitoring | Motor and ESC temperature logging through ESC telemetry | Build | S | SW |
| Noise measurement | Sound level at set distances for site permission and neighbours | Build | S | FT |
| Custom ESC | 18S, 150 A FOC inverter (see [baseline](baseline.md#4-custom-esc-designing-our-own-on-existing-chips)) | Buy → Build | L | ELEC |

---

## 3. Energy storage

| Item | What it involves | Build/buy | Scope | Owner |
| --- | --- | --- | --- | --- |
| Cell selection testing | Capacity and 10 s pulse resistance on P50B, P60B, 40PL | Build | S | ELEC, FT |
| Pack mechanical design | Cell holders, nickel-copper bus strips, compression, enclosure | Build | L | ELEC, MECH |
| Pack assembly process | Spot or laser welding, insulation, inspection checklist | Build | M | MFG |
| BMS hardware | Per-pack board on ADI ADBMS1818 with isoSPI to a pack MCU | Build | M | ELEC |
| BMS firmware | State of charge, balancing, temperature limits, fault reporting over CAN | Build | M | ELEC, SW |
| Pack thermal | Temperature sensors, downwash cooling ducts, thermal test at hover current | Build | M | ELEC, MECH |
| Charger and charging station | Balance charging to UT EHS rules, fire-safe storage | Buy | S | ELEC |
| Pack log | Cycle counts, capacity checks, incidents (Article X, Section 7) | Build | S | SW |
| Battery test bench | Programmable load or cycler for capacity and thermal tests | Buy/Build | M | ELEC, FT |

---

## 4. Power distribution and high voltage

| Item | What it involves | Build/buy | Scope | Owner |
| --- | --- | --- | --- | --- |
| Power distribution unit | Bus bars, per-pack contactors (Gigavac GX14), pre-charge, EV fuses | Build (COTS parts) | L | ELEC |
| Pre-charge and contactor driver board | Pre-charge sequencing, coil drivers, weld detection | Buy → Build | M | ELEC |
| Current sensing | Per-pack and per-branch current measurement | Build | M | ELEC |
| Hardwired E-stop loop | Pilot and ground E-stops open every contactor with no software in the path | Build | M | ELEC |
| HV interlock loop | Connectors that open the loop if unplugged | Build | S | ELEC |
| Insulation monitoring | Detects leakage from the HV bus to the frame (Bender iso165C class) | Buy | S | ELEC |
| Arming key switch | Physical key required to energize the HV bus | Buy | S | ELEC |
| Vehicle wiring harness | HV, LV, and CAN wiring between every subsystem, routed through folding arms, with harness drawings | Build | L | ELEC |
| Redundant DC-DC | Two isolated converters from different packs, ORed (Vicor DCM3623) | Buy → Build | M | ELEC |
| Low-voltage buck board | 75 V to 12 V on ADI LTC7801 | Build | M | ELEC |
| LV distribution board | 12 V and 5 V rails with eFuses and per-load switching and monitoring | Build | M | ELEC |
| Subscale buck converter | 6S to 5 V, 3 A for the subscale avionics | Build | S | ELEC |
| Subscale PDB | Fused 6S power distribution with voltage tap | Build | S | ELEC |
| Ground power and charge port | Safe connection for bench power and charging on the vehicle | Build | S | ELEC |

---

## 5. Avionics hardware

### Commercial flight stack (flies on the piloted vehicle)

| Item | Part | Owner |
| --- | --- | --- |
| Flight controller | Holybro Pixhawk 6X Pro (STM32H753, triple IMU incl. ADIS16470) | SW |
| GNSS and compass | 2 × CubePilot Here4 (u-blox F9P) | SW |
| Altitude | LightWare LW20/C LiDAR | SW |
| Telemetry radio | RFD900x | SW |
| RC link | ExpressLRS 900 MHz | SW |

### Custom avionics PCBs (Buy → Build)

These run first on the subscale drone and HIL rig, then on the unmanned article. Most are DroneCAN nodes, so they plug into the same bus as the commercial parts and can be swapped in one at a time.

| Board | Key parts (candidates) | Purpose | Scope |
| --- | --- | --- | --- |
| **Flight controller** | STM32H7 MCU; 3 IMUs from different makers (e.g. TDK ICM-45686, TDK IIM-42652, ADI ADIS16470); 2 barometers (e.g. TDK ICP-20100, Bosch BMP390); magnetometer (PNI RM3100); 2 × CAN FD transceivers; FRAM and microSD logging; isolated, redundant power inputs; IMU heater | Runs PX4 or ArduPilot on a real-time OS. Largest board the club builds. | L |
| **GNSS and compass node** | u-blox F9P or M10 module; magnetometer (RM3100 or ST IIS2MDC); STM32 MCU; CAN transceiver | Position and heading, mounted away from power wiring | M |
| **Barometer node** | 2 barometers in a shielded, vented enclosure; STM32; CAN | Altitude away from rotor pressure noise | S |
| **IMU breakout and vibration logger** | IMU plus logging MCU | Measure frame vibration at candidate mounting spots | S |
| **Optical flow node** | PixArt PMW3901 flow sensor plus ST VL53L1X time-of-flight sensor; STM32; CAN | Velocity and height aiding near the ground | S |
| **Power monitor node** | Isolated voltage and current measurement per pack; CAN | Pack power data to the flight controller and ground | M |
| **Pilot controls interface** | Hall-effect stick and throttle inputs, switch inputs, dual-redundant ADC; CAN | Converts pilot inputs to CAN commands | M |
| **Cockpit display board** | MCU with display driver; CAN | Battery, time remaining, altitude, warnings | M |
| **Lighting board** | High-brightness LED drivers; CAN | Anti-collision strobes and status lights | S |
| **Safety monitor board** | Lattice MachXO3D or Microchip IGLOO2 FPGA; own IMU; own isolated power and reset supervisor; contactor and parachute trigger drivers; heartbeat inputs | The independent monitor, as a flight unit | L |
| **Companion carrier board** | Carrier for the AMD Kria K26: MIPI camera inputs, Ethernet, CAN, USB, power | Hosts cameras and perception | L |
| **CAN bus tools** | USB-to-CAN adapter, bus termination and breakout boards | Bench debugging for every team | S |

---

## 6. Flight software

| Item | What it involves | Scope | Owner |
| --- | --- | --- | --- |
| Autopilot selection and setup | PX4 vs ArduPilot trade, coaxial X8 mixer, frame parameters | M | SW |
| Coaxial X8 simulation | Software-in-the-loop model including motor-out and pack-out | M | SW |
| State estimation tuning | EKF with IMU, barometer, GNSS, magnetometer, LiDAR, optical flow | M | SW |
| Attitude and rate control tuning | Single-axis rig, subscale, then tethered full scale | L | SW |
| Pilot control mapping | Fly-by-wire: stick to attitude, altitude hold, rate limits, expo | M | SW |
| Envelope protection | Tilt, climb and descent rate, altitude, and geofence limits | M | SW |
| Failsafes | Motor out, pack out, link loss, GNSS loss, low battery, controlled auto-land | L | SW |
| Motor failure detection | From ESC current and RPM telemetry | M | SW |
| Arming logic | Arm locks, key switch, pre-flight checks, health checks before arming | M | SW |
| DroneCAN node firmware | Shared firmware base for all custom CAN boards | M | SW, ELEC |
| Vibration analysis | IMU spectrum logs to tune filters and mounts | S | SW |
| Log analysis pipeline | Automatic plots and pass/fail checks after every test | M | SW, FT |
| Parameter configuration control | Every flown parameter set version-controlled and tied to a test | S | SYS |

---

## 7. Perception and cameras

All of this runs on the companion computer and is **advisory only** on the piloted vehicle: it can inform the pilot, the ground crew, and the logs, but it has no path to motor command (Article X, Section 10). On the unmanned test articles, it may be tested as an aiding input to navigation under a test procedure.

| Item | What it involves | Scope | Owner |
| --- | --- | --- | --- |
| Camera selection | Global-shutter cameras (OV9281 class): downward, forward, and cockpit-facing | S | SW |
| Camera mounting and sync | Vibration isolation, hardware trigger sync with the IMU | M | SW, MECH |
| Optical flow | Downward flow for velocity over ground, first on the dedicated flow node, then camera-based | M | SW |
| Visual-inertial odometry | Position and velocity from camera plus IMU (OpenVINS or VINS-Fusion) | L | SW |
| SLAM and mapping | Map of the test site (ORB-SLAM3 class) for position logging and site survey | L | SW |
| Obstacle detection | Depth or scanning LiDAR (LightWare SF45/B class) to warn the pilot | L | SW |
| Landing zone check | Downward camera flags slope and obstacles below | M | SW |
| Video downlink | Live forward and pilot camera feed to the ground station | M | SW |
| FPGA image preprocessing | Camera capture, undistortion, and feature detection on the Kria FPGA fabric | L | SW |
| ROS 2 software stack | Middleware for perception, logging, and telemetry on the companion | M | SW |
| Dataset capture | Synchronized camera, IMU, GNSS logs from subscale flights for offline development | S | SW, FT |

---

## 8. Safety systems

| Item | What it involves | Build/buy | Scope | Owner |
| --- | --- | --- | --- | --- |
| Safety monitor RTL | Heartbeat, attitude, rate, and power limits; authority to cut power and fire the parachute | Build | L | SW |
| Heartbeat watchdog | First RTL on a dev board: trip an output when a heartbeat stops | Build | S | SW |
| Ballistic parachute | Galaxy GRS 3 270, mounting, trigger circuit | Buy | M | MECH, ELEC |
| Parachute deployment study | Minimum safe deployment height from hover; sets the flight envelope | Build | M | FT, SYS |
| Fire safety | Battery bay barriers, fire suppression at the range, thermal runaway response plan | Build | M | ELEC, FT |
| Tether system (Phase 2) | Tether rig, load cell, quick release, anchor design | Build | L | FT, MECH |
| Hazard analysis | Functional hazard assessment and FMEA, kept current | Build | L | SYS |
| Pre-flight and arming checklists | Written checks, tied to arming logic | Build | S | FT |

---

## 9. Cockpit and pilot

| Item | What it involves | Scope | Owner |
| --- | --- | --- | --- |
| Seat and restraint | Carbon seat shell, 4 or 5-point harness, mounts sized for crash loads | M | MECH |
| Pilot controls | Hall-effect side stick and throttle, ergonomic placement | M | MECH, ELEC |
| Emergency controls | E-stop and parachute handle placement, guarded against accidental use | S | MECH |
| Pilot display and alerts | Battery and time remaining, altitude, warnings, audio alerts | M | SW, ELEC |
| Pilot communications | Helmet intercom with the ground crew | S | FT |
| Ingress and egress | Getting in and out quickly, including emergency exit | S | MECH |
| Pilot training simulator | Flight simulator with the real stick and display, driven by the vehicle model | M | SW |
| Pilot training and currency program | Required by Article X, Section 5(f) | M | FT |

---

## 10. Ground station and telemetry

| Item | What it involves | Scope | Owner |
| --- | --- | --- | --- |
| Ground control software | QGroundControl or Mission Planner configuration | S | SW |
| Test dashboard | Pack, motor, and temperature displays with limits | M | SW |
| Range safety officer E-stop | Independent radio link that opens the E-stop loop | M | ELEC |
| Video and telemetry recording | Everything from every test saved with the configuration used | S | SW, FT |

---

## 11. Test infrastructure

| Item | What it involves | Scope | Owner |
| --- | --- | --- | --- |
| Thrust stand | Single and coaxial configurations, load cell rated past 80 kgf | M | MFG, FT |
| Current and voltage sense board | Motor current and voltage logging on the thrust stand | S | ELEC |
| Thrust stand data logger | Records load cell, RPM, current, voltage; plots efficiency | S | SW |
| HIL rig | Real flight controller and safety monitor against a simulated vehicle | L | SW |
| Tether test rig | See safety systems | L | FT |
| Data acquisition kit | Accelerometers, strain gauges, microphones, synchronized logging | M | FT |
| Range kit | Charging, power, fire suppression, communications, weather station | M | FT |

---

## 12. Subscale demonstrator (Phase 1)

| Item | What it involves | Scope | Owner |
| --- | --- | --- | --- |
| Subscale airframe | 1/3 scale coaxial X8, 15 in props, about 5 kg | M | MECH, MFG |
| Subscale power | 6S LiPo, team-built buck converter and PDB | S | ELEC |
| Avionics testbed | Commercial flight controller first, then custom boards one at a time | M | SW, ELEC |
| Configuration comparison | Fly as coaxial X8 and flat octo to compare efficiency and motor-out | M | FT |

---

## 13. Systems, regulatory, and program

| Item | What it involves | Scope | Owner |
| --- | --- | --- | --- |
| Requirements | Vehicle and subsystem requirements with rationale | M | SYS |
| Interface control documents | Mechanical, power, data, and CAN message definitions between subsystems | M | SYS |
| Budgets | Mass, power, thermal, and CAN bus load | M | SYS |
| Verification matrix | Every requirement mapped to the test or analysis that closes it | M | SYS |
| Configuration management | Design baseline, change log, flown-configuration records | S | SYS |
| Configuration trade study | See [configuration-trade-study.md](configuration-trade-study.md) | M | SYS |
| Part 103 conformance | Weight, speed, and operations evidence; FAA question on parachute exclusion | M | SYS |
| Design reviews | PDR, CDR, and Test Readiness Reviews | L | SYS |
| Insurance and site permissions | Coverage and written site permissions (Article X, Section 5) | M | BIZ, FT |
| Sponsorship and budget | Funding for the roughly $20,000 to $22,000 hardware estimate | L | BIZ |

---

## Custom PCB summary

Every board the club plans to design, in rough order of when it's needed:

| Board | Section | Scope | Flies piloted? |
| --- | --- | --- | --- |
| Subscale buck converter (6S to 5 V) | 4 | S | No (subscale) |
| Subscale PDB | 4 | S | No (subscale) |
| Current and voltage sense board | 11 | S | No (test rig) |
| IMU breakout and vibration logger | 5 | S | No (test tool) |
| CAN bus tools | 5 | S | No (test tool) |
| Barometer node | 5 | S | After qualification |
| Optical flow node | 5 | S | After qualification |
| Lighting board | 5 | S | After qualification |
| GNSS and compass node | 5 | M | After qualification |
| Power monitor node | 5 | M | After qualification |
| Pilot controls interface | 5 | M | After qualification |
| Cockpit display board | 5 | M | After qualification |
| 18S BMS board | 3 | M | After qualification |
| Low-voltage buck board | 4 | M | After qualification |
| LV distribution board | 4 | M | After qualification |
| Pre-charge and contactor driver | 4 | M | After qualification |
| Flight controller | 5 | L | After qualification |
| Safety monitor board | 5 | L | After qualification |
| Companion carrier board | 5 | L | Advisory only |
| Custom ESC | 2 | L | Not this program cycle |

"After qualification" means the board is allowed to replace its commercial counterpart only after it clears Article X, Section 3 and logs run time on the unmanned article.
