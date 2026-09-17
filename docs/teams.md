# Teams and Projects

Every Longhorn eVTOL engineering team, its sub-teams, and the projects each sub-team owns. Every sub-team has smaller projects that are good ways to learn the tools and the vehicle, alongside the larger design work. Projects are sized **S** (a few weeks for a small group), **M** (about a semester), or **L** (multiple semesters), and tagged by the vehicle they serve:

| Tag | Meaning |
| --- | --- |
| **Subscale** | The [1/3-scale demonstrator](vehicle/subscale-demonstrator.md), the current build |
| **Full scale** | The [piloted vehicle](vehicle/baseline.md) |
| **Both** | Built once, used on both |

New to this? Start with the [onboarding ladder](onboarding/), which is open to anyone, member or not.

Many projects span teams; each is listed under the sub-team that leads it. Every custom board and flight-critical design follows the [safety requirements](safety/vehicle-safety-requirements.md). The full parts and interface picture is in [`subsystems.md`](vehicle/subsystems.md).

**Jump to:** [Mechanical Design](#mechanical-design) · [Electrical and Power](#electrical-and-power) · [Software and Avionics](#software-and-avionics) · [Manufacturing and Operations](#manufacturing-and-operations) · [Flight Test and Range Operations](#flight-test-and-range-operations) · [Systems Engineering and Integration](#systems-engineering-and-integration)

---

## Mechanical Design

Designs the structure, cockpit, propulsion mounts, and landing gear. Every part answers to the 254 lb empty-weight limit.

### Airframe and Chassis

The structural skeleton, load paths, and FEA in CAD.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Subscale airframe | Carbon plates and 25 mm folding arms at a 711 mm motor diagonal, pack bays and parachute mount in full-scale positions | M | Subscale | [`mechanical/subscale-airframe`](../mechanical/subscale-airframe) |
| Folding arm joint and lock | Hinge that locks rigid in flight and folds for transport, with a lock sensor the flight controller checks before arming | M | Both | [`mechanical/airframe/folding-arm-joint`](../mechanical/airframe/folding-arm-joint) |
| Fold-cycle fatigue rig | Cycle the fold joint beyond expected ground and transport life, then measure wear and free play against the arm-joint acceptance limits | M | Both | [`mechanical/airframe/folding-arm-joint`](../mechanical/airframe/folding-arm-joint) |
| Battery bays | Four bays with fire barriers, retention, cooling air paths, and quick removal | M | Both | [`mechanical/airframe/battery-bays`](../mechanical/airframe/battery-bays) |
| Fire-barrier panel test | Contain and route a single-cell thermal-runaway event for a stated duration, measure temperatures, and confirm the vent path is directed away from the occupant | M | Both | [`mechanical/airframe/battery-bays`](../mechanical/airframe/battery-bays) |
| Parachute mount | Hard points and a load path sized for deployment shock | M | Both | [`mechanical/airframe/parachute-mount`](../mechanical/airframe/parachute-mount) |
| Centre cage | 4130 chromoly tube frame carrying seat, packs, parachute, and avionics | L | Full scale | [`mechanical/airframe`](../mechanical/airframe) |
| Avionics bay | Vibration-isolated mounting for the flight controller and safety monitor | S | Both | [`mechanical/airframe`](../mechanical/airframe) |
| Structural analysis | FEA for 2 g maneuvering, hard landing, motor out, parachute deployment, and arm lock; modal analysis to keep frame resonances away from rotor speeds; arm joint fatigue | L | Both | [`mechanical/airframe`](../mechanical/airframe) |

### Seating, Ergonomics, and Cockpit

Pilot seat, harness mounts, controls, and cockpit layout.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Ballast tray | 2.7 kg adjustable pilot ballast at the seat position, shiftable fore-aft and side to side | S | Subscale | [`mechanical/cockpit-ergonomics`](../mechanical/cockpit-ergonomics) |
| Seat and restraint | Carbon seat shell and 4- or 5-point harness mounts sized for crash loads | M | Full scale | [`mechanical/cockpit-ergonomics`](../mechanical/cockpit-ergonomics) |
| Pilot controls layout | Placement of the hall-effect side stick and throttle | M | Full scale | [`mechanical/cockpit-ergonomics`](../mechanical/cockpit-ergonomics) |
| Emergency controls | E-stop and parachute handle placement, guarded against accidental use | S | Full scale | [`mechanical/cockpit-ergonomics`](../mechanical/cockpit-ergonomics) |
| Ingress and egress | Getting in and out quickly, including emergency exit | S | Full scale | [`mechanical/cockpit-ergonomics`](../mechanical/cockpit-ergonomics) |

### Propulsion and Duct

Motor mounts, rotor spacing, guards, and ducts.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Motor mount bracket | CAD, hand calcs, and FEA, then machine it at Texas Inventionworks | S | Subscale | [`mechanical/propulsion-duct`](../mechanical/propulsion-duct) |
| Coaxial motor mounts | Upper motor on top, lower motor inverted underneath, tuned for vibration, rotor gap matched between scales | M | Both | [`mechanical/propulsion-duct`](../mechanical/propulsion-duct) |
| Coaxial spacing study | Rotor gap vs efficiency on the thrust stand, with Flight Test | M | Both | [`mechanical/propulsion-duct`](../mechanical/propulsion-duct) |
| Rotor clearance envelope | Worst-case deflected tip-gap analysis across thrust, joint play, hinge wear, and thermal growth, for single-rotor and coaxial pairs | M | Both | [`mechanical/propulsion-duct`](../mechanical/propulsion-duct) |
| Rotor guards or ducts | Trade study: guards vs ducts vs open rotors, mass vs protection | M | Full scale | [`mechanical/propulsion-duct`](../mechanical/propulsion-duct) |

### Landing Gear and Suspension

Skids and structures that absorb hard landings.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Subscale skids | Scaled landing gear for the demonstrator | S | Subscale | [`mechanical/landing-gear`](../mechanical/landing-gear) |
| Landing skid drop test | Size skids for hard landing loads and verify on a drop rig | M | Both | [`mechanical/landing-gear`](../mechanical/landing-gear) |

---

## Electrical and Power

Batteries, power distribution, motor control, safety interlocks, and the club's custom circuit boards. Boards are designed in KiCad and hand-assembled ([PCB design guide](hardware/pcb-design-guide.md), [avionics parts](vehicle/avionics-parts.md)).

### Power Distribution and Wiring Harness

Battery packs, bus bars, contactors, and wiring between systems.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Connector and crimp standard | Crimp, solder, and heat-shrink AS150U, XT60, and JST-GH connections; pull-test samples and write the standard the harness builds follow | S | Both | [`electrical/pdu-harness/wiring-harness`](../electrical/pdu-harness/wiring-harness) |
| Harness continuity tester | Simple board or jig that checks every pin of a harness for opens, shorts, and swapped wires before it goes on the vehicle | S | Both | [`electrical/pdu-harness/wiring-harness`](../electrical/pdu-harness/wiring-harness) |
| **Pack switch board** | Club's first power board: back-to-back MOSFET switch, pre-charge, 80 A fuse, telemetry, E-stop and safety monitor enable ([design](hardware/pack-switch-board.md)) | M | Subscale | [`electrical/pcb/pack-switch-board`](../electrical/pcb/pack-switch-board) |
| Subscale power distribution board | Fused 6S distribution with a voltage tap | S | Subscale | [`electrical/pcb/subscale-pdb`](../electrical/pcb/subscale-pdb) |
| Battery pack design | Cell holders, nickel-copper bus strips, compression, and enclosure for Molicel P50B packs | L | Both | [`electrical/pdu-harness`](../electrical/pdu-harness) |
| Battery management system | ADBMS1818 board plus firmware: state of charge, balancing, temperature limits, fault reports over CAN, automatic pack log | M | Full scale | [`electrical/pcb/bms-18s`](../electrical/pcb/bms-18s) |
| Pack thermal | Temperature sensing, downwash cooling paths, thermal test at hover current | M | Both | [`electrical/pdu-harness`](../electrical/pdu-harness) |
| Power distribution unit | Full-scale bus bars, Gigavac GX14 contactors, pre-charge, EV fuses, per-branch current sensing | L | Full scale | [`electrical/pdu-harness/power-distribution-unit`](../electrical/pdu-harness/power-distribution-unit) |
| Vehicle wiring harness | Power, low-voltage, and CAN wiring between packs, ESCs, flight computers, and cockpit, routed through folding arms, with harness drawings | L | Both | [`electrical/pdu-harness/wiring-harness`](../electrical/pdu-harness/wiring-harness) |
| Charging station | Balance charging and fire-safe storage | S | Both | [`electrical/pdu-harness`](../electrical/pdu-harness) |

### ESC and Motor Integration

Motor controllers, signal wiring, and motor telemetry.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| ESC bench test setup | Current-limited supply, servo tester, and a small motor for checking ESC direction, calibration, and DroneCAN telemetry before installation | S | Subscale | [`electrical/esc-motor`](../electrical/esc-motor) |
| Motor constant measurement | Measure Kv, winding resistance, and no-load current of each MN5008 and record them against the datasheet | S | Subscale | [`electrical/esc-motor`](../electrical/esc-motor) |
| Subscale propulsion integration | MN5008 motors with Zubax Myxa ESCs on DroneCAN, pack current caps, CW/CCW mapping | M | Subscale | [`electrical/esc-motor`](../electrical/esc-motor) |
| Full-scale propulsion integration | Hobbywing X13 G2 units on DroneCAN, telemetry, thermal limits | M | Full scale | [`electrical/esc-motor`](../electrical/esc-motor) |
| Current and voltage sense board | Motor current and voltage logging on the thrust stand | S | Both | [`electrical/pcb/current-sense`](../electrical/pcb/current-sense) |
| Custom ESC | 18S, 150 A FOC inverter on 150 V OptiMOS FETs with VESC firmware; dyno and subscale first, then unmanned only | L | Both | [`electrical/esc-motor/custom-esc`](../electrical/esc-motor/custom-esc) |

### Avionics and Power Architecture

Low-voltage power for flight computers, sensors, and the cockpit.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Avionics power measurement | Measure the real current draw of every avionics part (flight controller, GNSS, radios, Jetson, cameras) with a current monitor and feed the numbers into the power budget | S | Subscale | [`electrical/power-architecture`](../electrical/power-architecture) |
| Buck converter board | Design, lay out, and bring up a 6S to 5 V, 3 A converter for the subscale avionics | S | Subscale | [`electrical/pcb/buck-converter`](../electrical/pcb/buck-converter) |
| Low-voltage buck board | 75 V to 12 V avionics supply on an LTC7801, redundant with commercial modules | M | Full scale | [`electrical/pcb/lv-buck`](../electrical/pcb/lv-buck) |
| Redundant avionics power | Two converters fed from different packs, ORed with ideal diodes | M | Both | [`electrical/power-architecture`](../electrical/power-architecture) |
| LV distribution board | 12 V and 5 V rails with eFuses and per-load monitoring | M | Both | [`electrical/pdu-harness/lv-distribution`](../electrical/pdu-harness/lv-distribution) |
| Power monitor node | Per-pack voltage and current on DroneCAN (INA228 and shunt) | M | Both | [`avionics-pcb/power-monitor-node`](../avionics-pcb/power-monitor-node) |

### Safety Interlocks and E-Stop

Kill switches, emergency disconnects, and parachute triggers.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| E-stop test box | Handheld box with a mushroom E-stop, loop connector, and indicator LED used on every bench and thrust stand test | S | Both | [`electrical/safety-interlocks`](../electrical/safety-interlocks) |
| Arming status light | Bright LED indicator on the vehicle showing disarmed, armed, and fault states from the E-stop loop and flight controller | S | Subscale | [`electrical/safety-interlocks`](../electrical/safety-interlocks) |
| Hardwired E-stop loop | Pilot and ground E-stops open every pack with no software in the path | M | Both | [`electrical/safety-interlocks`](../electrical/safety-interlocks) |
| Pre-charge and contactor driver | Pre-charge sequencing, coil drivers, and weld detection for each full-scale pack | M | Full scale | [`electrical/safety-interlocks`](../electrical/safety-interlocks) |
| Parachute trigger circuit | Arm/safe switch, dual firing channels, continuity check | M | Both | [`electrical/safety-interlocks`](../electrical/safety-interlocks) |
| HV interlock and arming | Interlock loop through connectors, physical arming key | S | Full scale | [`electrical/safety-interlocks`](../electrical/safety-interlocks) |
| Insulation monitoring | Detect leakage from the high-voltage bus to the frame | S | Full scale | [`electrical/safety-interlocks`](../electrical/safety-interlocks) |

### Custom PCB and Hardware Design

Schematics, KiCad layout, assembly, and bring-up of club boards. Commercial parts fly first; each club board replaces its commercial counterpart only after [qualification](safety/vehicle-safety-requirements.md#s2-custom-hardware-qualification).

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Footprint verification | Print new KiCad footprints at 1:1, check every part against its land pattern, and sign off entries in the club library | S | Both | [`hardware-lib`](../hardware-lib) |
| Sensor breakout boards | Small breakouts for the BMP581 barometer and ICM-42688-P IMU to prove footprints, assembly, and driver code before the real boards | S | Both | [`avionics-pcb/barometer-node`](../avionics-pcb/barometer-node) |
| Shared KiCad library | Club symbols, footprints, and 3D models with manufacturer part numbers | S | Both | [`hardware-lib`](../hardware-lib) |
| IMU vibration logger | Measure frame vibration at candidate mounting spots | S | Both | [`avionics-pcb/imu-vibration-logger`](../avionics-pcb/imu-vibration-logger) |
| CAN bus tools | USB-to-CAN adapter, termination, and breakout boards for bench work | S | Both | [`avionics-pcb/can-tools`](../avionics-pcb/can-tools) |
| Barometer node | Two Bosch BMP581 barometers in a vented enclosure on DroneCAN | S | Both | [`avionics-pcb/barometer-node`](../avionics-pcb/barometer-node) |
| Optical flow node | PixArt PAA3905 flow sensor and Broadcom AFBR-S50 distance sensor on DroneCAN | S | Both | [`avionics-pcb/optical-flow-node`](../avionics-pcb/optical-flow-node) |
| Lighting board | Anti-collision strobes and status lights | S | Both | [`avionics-pcb/lighting`](../avionics-pcb/lighting) |
| GNSS and compass node | u-blox ZED-F9P plus magnetometer on DroneCAN | M | Both | [`avionics-pcb/gnss-compass-node`](../avionics-pcb/gnss-compass-node) |
| Pilot controls interface | Hall-effect stick and throttle inputs on two redundant channels to CAN | M | Full scale | [`avionics-pcb/pilot-controls-interface`](../avionics-pcb/pilot-controls-interface) |
| Cockpit display board | Battery, time remaining, altitude, and warnings for the pilot | M | Full scale | [`avionics-pcb/cockpit-display`](../avionics-pcb/cockpit-display) |
| Flight controller board | STM32H753 with three IMUs, two barometers, magnetometer, dual CAN FD; Pixhawk FMUv6X-compatible | L | Both | [`avionics-pcb/flight-controller`](../avionics-pcb/flight-controller) |
| Safety monitor board | IGLOO2 FPGA with its own IMU, isolated power, window watchdog, contactor and parachute drivers | L | Both | [`avionics-pcb/safety-monitor-board`](../avionics-pcb/safety-monitor-board) |
| Companion carrier board | Carrier for the Jetson Orin NX with camera inputs, Ethernet, and CAN | L | Full scale | [`avionics-pcb/companion-carrier`](../avionics-pcb/companion-carrier) |

---

## Software and Avionics

Flight control, state estimation, cameras, ground station, failsafes, and FPGA work.

### Flight Control

Guidance, navigation, control loops, and tuning.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Simulation setup guide | Get PX4 or ArduPilot software-in-the-loop running with the coaxial X8 frame and write the setup guide the whole team uses | S | Both | [`software/flight-control`](../software/flight-control) |
| PID tuning notebook | Python simulation of a single-axis PID loop with motor lag and sensor noise, showing how gains change overshoot and settling | S | Both | [`software/flight-control`](../software/flight-control) |
| Coaxial X8 simulation | Software-in-the-loop model covering motor-out and pack-out cases | M | Both | [`software/flight-control`](../software/flight-control) |
| Autopilot setup | PX4 vs ArduPilot choice, coaxial X8 mixer, frame parameters | M | Both | [`software/flight-control`](../software/flight-control) |
| Single-axis PID rig | Tune attitude control on a one-degree-of-freedom bench | S | Subscale | [`software/flight-control`](../software/flight-control) |
| Attitude and rate tuning | Subscale first, then tethered full scale | L | Both | [`software/flight-control`](../software/flight-control) |
| Pilot control mapping | Fly-by-wire stick to attitude, altitude hold, rate limits | M | Full scale | [`software/flight-control/pilot-mapping`](../software/flight-control/pilot-mapping) |
| Envelope protection | Tilt, climb and descent rate, altitude, and geofence limits | M | Both | [`software/flight-control`](../software/flight-control) |

### Sensor Fusion and Telemetry

State estimation from IMU, GNSS, barometer, and cameras, plus telemetry.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| IMU vibration plots | Script that reads vibration logger files and produces spectrum plots so Mechanical can compare mounting spots | S | Both | [`software/sensor-fusion-telemetry`](../software/sensor-fusion-telemetry) |
| Barometer noise study | Log both barometers on the bench and in prop wash, then quantify noise and drift to set estimator parameters | S | Subscale | [`software/sensor-fusion-telemetry`](../software/sensor-fusion-telemetry) |
| Complementary filter demo | Estimate attitude from logged IMU data with a complementary filter and compare it with the flight controller estimate | S | Both | [`software/sensor-fusion-telemetry`](../software/sensor-fusion-telemetry) |
| Thrust stand data logger | Record load cell, RPM, current, and voltage, then plot thrust and efficiency curves | S | Both | [`software/thrust-stand-logger`](../software/thrust-stand-logger) |
| State estimation tuning | EKF fusing IMU, barometer, GNSS, magnetometer, LiDAR, and optical flow | M | Both | [`software/flight-control/state-estimation`](../software/flight-control/state-estimation) |
| Vibration analysis | IMU spectrum logs to tune filters and mounts | S | Both | [`software/sensor-fusion-telemetry`](../software/sensor-fusion-telemetry) |
| DroneCAN node firmware | Shared firmware base for every club CAN board | M | Both | [`software/dronecan-firmware`](../software/dronecan-firmware) |
| Camera integration | Global-shutter downward and forward cameras hardware-synced to the IMU, with Kalibr calibration | M | Both | [`software/perception/cameras`](../software/perception/cameras) |
| Optical flow | Velocity over ground from the downward camera and flow sensor | M | Both | [`software/perception/optical-flow`](../software/perception/optical-flow) |
| Visual-inertial odometry | Position and velocity from camera plus IMU (OpenVINS or VINS-Fusion), advisory only | L | Both | [`software/perception/vio-slam`](../software/perception/vio-slam) |
| SLAM and mapping | Map of the test site for position logging and site surveys | L | Both | [`software/perception/vio-slam`](../software/perception/vio-slam) |
| Dataset capture | Synchronized camera, IMU, and GNSS logs from subscale flights | S | Subscale | [`software/perception`](../software/perception) |

### Cockpit Interface and Ground Station

Pilot display and ground station software.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Flight time remaining estimator | Predict remaining hover time from pack voltage and current logs, validated against thrust stand and flight data | S | Both | [`software/ground-station`](../software/ground-station) |
| Digital pre-flight checklist | Small app for running and recording pre-flight and arming checklists, saved with each test log | S | Both | [`software/ground-station`](../software/ground-station) |
| Ground station dashboard | Live MAVLink telemetry with pack, motor, and temperature limits | M | Both | [`software/ground-station`](../software/ground-station) |
| Video downlink | Live forward and pilot camera feeds to the ground station | M | Both | [`software/perception/video-downlink`](../software/perception/video-downlink) |
| Obstacle warnings | Scanning LiDAR or depth camera warnings to the pilot, advisory only | L | Both | [`software/perception/obstacle-detection`](../software/perception/obstacle-detection) |
| Pilot display software | Battery, time remaining, altitude, warnings, and audio alerts | M | Full scale | [`software/ground-station`](../software/ground-station) |
| Pilot training simulator | Flight simulator using the real stick and display, driven by the vehicle model | M | Full scale | [`software/pilot-simulator`](../software/pilot-simulator) |

### Fault Management and Failsafes

Motor-out handling, recovery logic, and geofencing.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Flight log parser | Script that pulls motor outputs, battery data, and warnings out of PX4 or ArduPilot logs into tables and plots; the first piece of the log pipeline | S | Both | [`software/fault-management`](../software/fault-management) |
| Failsafe simulation tests | Scripts that trigger link loss, GNSS loss, and low battery in simulation and record how the vehicle responds | S | Both | [`software/fault-management`](../software/fault-management) |
| Failsafe logic | Motor out, pack out, link loss, GNSS loss, and low battery, each ending in a controlled landing | L | Both | [`software/fault-management`](../software/fault-management) |
| Motor failure detection | Detect a failed motor from ESC current and RPM telemetry | M | Both | [`software/fault-management`](../software/fault-management) |
| Arming logic | Arm locks, key switch, and health checks required before arming | M | Both | [`software/fault-management`](../software/fault-management) |
| Log analysis pipeline | Automatic plots and pass/fail checks after every test | M | Both | [`software/fault-management`](../software/fault-management) |

### Hardware Acceleration and FPGA

Safety monitor, hardware-in-the-loop rig, and camera processing on FPGAs. Deliberately limited to these three workstreams: stabilization and motor command stay on the flight controller, where control loop latency is set by sensor filtering, ESC update rate, and propeller inertia rather than compute.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| FPGA bring-up tutorials | Blink, UART, and a simple state machine on the iCEBreaker with open-source tools, written up as the path into safety monitor work | S | Subscale | [`fpga/heartbeat-watchdog`](../fpga/heartbeat-watchdog) |
| PWM pulse decoder | Verilog module and testbench that measures RC and servo pulse widths, reused as a safety monitor input | S | Both | [`fpga/safety-monitor`](../fpga/safety-monitor) |
| Heartbeat watchdog | First RTL on a small FPGA board: trip an output when a heartbeat stops | S | Subscale | [`fpga/heartbeat-watchdog`](../fpga/heartbeat-watchdog) |
| Safety monitor RTL | Heartbeat, attitude, rate, and power limit checks with authority to cut power and fire the parachute, plus testbenches | L | Both | [`fpga/safety-monitor`](../fpga/safety-monitor) |
| Hardware-in-the-loop rig | Run flight software and the safety monitor against a simulated vehicle | L | Both | [`fpga/hil-rig`](../fpga/hil-rig) |
| FPGA image preprocessing | Camera capture and feature detection on an AMD Kria KV260 | L | Both | [`fpga/companion-computing`](../fpga/companion-computing) |

---

## Manufacturing and Operations

Turns designs into hardware: welding, composites, machining, and assembly. Shop work requires facility training first.

### Welding and Metal Fabrication

TIG welding and tube frame fabrication.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Weld coupons | Practice and test tube joints before any airframe welding | S | Full scale | [`manufacturing/welding-fab`](../manufacturing/welding-fab) |
| Centre cage fabrication | Weld the 4130 chromoly frame | L | Full scale | [`manufacturing/welding-fab`](../manufacturing/welding-fab) |
| Weld distortion control plan | Weld sequence and tack plan, fixture design, post-weld straightness check, and a rework-or-scrap tolerance band for the cage | M | Full scale | [`manufacturing/welding-fab`](../manufacturing/welding-fab) |

### Composites and Layup

Carbon fiber layup, vacuum bagging, and curing.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Composite arm coupons | Lay up carbon tube samples and test them to failure to set arm design allowables | M | Full scale | [`manufacturing/composites`](../manufacturing/composites) |
| Bonded-insert qualification | Pull-out and torque-out testing of arm inserts under hot/wet conditioning, since arm attachment points are the most failure-prone feature of a composite arm | M | Both | [`manufacturing/composites`](../manufacturing/composites) |
| Fairings and panels | Composite covers and the cockpit panel | M | Full scale | [`manufacturing/composites`](../manufacturing/composites) |

### Machining and CNC

Mills, lathes, and CNC at Texas Inventionworks.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| 3D-printed mounts and clips | Printed camera mounts, cable clips, and sensor brackets for the demonstrator, iterated from fit checks | S | Subscale | [`manufacturing/machining-cnc`](../manufacturing/machining-cnc) |
| Subscale frame parts | Cut carbon plates, arm clamps, and motor mounts for the demonstrator | S | Subscale | [`manufacturing/machining-cnc`](../manufacturing/machining-cnc) |
| Thrust stand frame | Rigid stand with a load cell rated past 80 kgf for single and coaxial testing | M | Both | [`mechanical/thrust-stand`](../mechanical/thrust-stand) |

### Assembly and Quality Control

Vehicle integration, torque specs, and inspections.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Torque and inspection checklists | The assembly checks every rollout uses | S | Both | [`manufacturing/assembly-qc`](../manufacturing/assembly-qc) |
| FAI and gauge plan | First-article inspection method and gauges for critical interfaces (arm clamp, motor mount, fold joint), run before each batch | S | Both | [`manufacturing/assembly-qc`](../manufacturing/assembly-qc) |
| Propeller balancing | Static and dynamic balancing procedure | S | Both | [`manufacturing/assembly-qc`](../manufacturing/assembly-qc) |
| Board assembly process | Stencil, reflow, and inspection workflow for club boards | M | Both | [`manufacturing/assembly-qc`](../manufacturing/assembly-qc) |
| Pack assembly process | Cell welding, insulation, and pack inspection | M | Both | [`manufacturing/assembly-qc`](../manufacturing/assembly-qc) |
| Transport cradle | Trailer fixture and ground handling points | S | Full scale | [`manufacturing/assembly-qc`](../manufacturing/assembly-qc) |

---

## Flight Test and Range Operations

Plans and runs tests safely, and turns test data into results.

### Test Planning

Test procedures, abort criteria, and emergency plans.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Test procedure template | Configuration, objectives, abort criteria, stations, exclusion zone, and emergency plan in one format | S | Both | [`flight-test/test-plans`](../flight-test/test-plans) |
| Subscale test campaign | Procedures for the [subscale test plan](vehicle/subscale-demonstrator.md#test-plan): tethered hover through motor-out and pack-out flights | M | Subscale | [`flight-test/test-plans`](../flight-test/test-plans) |
| Motor-out thermal test | Hold one full-scale unit at 40 to 45 kgf for 120 s and log ESC and motor temperature | S | Full scale | [`flight-test/test-plans/motor-out-thermal`](../flight-test/test-plans/motor-out-thermal) |
| Pre-flight and arming checklists | Written checks tied to the arming logic | S | Both | [`flight-test/test-plans`](../flight-test/test-plans) |

### Range and Logistics

Test sites, permissions, transport, and exclusion zones.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Test site survey | Find and document sites for subscale and tethered testing | S | Both | [`flight-test/range-logistics`](../flight-test/range-logistics) |
| Range kit | Charging, power, fire suppression, communications, weather station | M | Both | [`flight-test/range-logistics`](../flight-test/range-logistics) |
| Tether test rig | Tether, load cell, anchor, and quick release | L | Both | [`flight-test/tether-rig`](../flight-test/tether-rig) |

### Data and Instrumentation

Sensors on test stands and vehicles, data recording, and analysis.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Thrust stand calibration | Calibrate the load cell and current sensors with known weights and a reference meter, and document the procedure | S | Both | [`flight-test/data`](../flight-test/data) |
| Motor and prop characterization | Replace catalog numbers with thrust stand data, single and coaxial | S | Both | [`flight-test/data`](../flight-test/data) |
| Cell pulse resistance test | Compare candidate 21700 cells under 10 s pulses to pick the pack cell | S | Both | [`flight-test/data`](../flight-test/data) |
| Parachute deployment study | Minimum safe deployment height from a hover, which sets the flight envelope | M | Both | [`flight-test/data`](../flight-test/data) |
| Data acquisition kit | Accelerometers, strain gauges, microphones, synchronized logging | M | Both | [`flight-test/data`](../flight-test/data) |
| Noise measurement | Sound levels at set distances | S | Both | [`flight-test/data`](../flight-test/data) |

---

## Systems Engineering and Integration

Keeps requirements, interfaces, and budgets consistent across the vehicle.

### Requirements and Interfaces

Requirements, mass and power budgets, and interface documents.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Mass budget tracker | Keep the 254 lb budget (and the subscale budget) current using measured part weights, and flag growth against the limit | S | Both | [`systems/mass-budget`](../systems/mass-budget) |
| Requirements | Vehicle and subsystem requirements with rationale | M | Both | [`systems/requirements`](../systems/requirements) |
| Interface control documents | Mechanical, power, data, and CAN message definitions between teams | M | Both | [`systems/interfaces`](../systems/interfaces) |
| Power, thermal, and CAN budgets | Budgets beyond mass, kept alongside the requirements | M | Both | [`systems/requirements`](../systems/requirements) |
| Verification matrix | Every requirement mapped to the analysis or test that closes it | M | Both | [`systems/requirements`](../systems/requirements) |
| Hazard analysis | Functional hazard assessment and FMEA, kept current as the design changes | L | Both | [`systems/hazard-analysis`](../systems/hazard-analysis) |
| Part 103 conformance | Weight, speed, and operations evidence for the piloted vehicle | M | Full scale | [`systems/requirements`](../systems/requirements) |

### Configuration Management

The design baseline, change log, and records of what flew.

| Project | What it involves | Size | Vehicle | Folder |
| --- | --- | :---: | --- | --- |
| Design baseline and change log | So the configuration flown is always the configuration reviewed | S | Both | [`systems/configuration-management`](../systems/configuration-management) |
| Parameter configuration control | Every flown parameter set version-controlled and tied to a test | S | Both | [`systems/configuration-management`](../systems/configuration-management) |
| Configuration trade study | Coaxial X8 vs flat octo vs coaxial X12, settled by thrust stand and subscale tests ([study](vehicle/configuration-trade-study.md)) | M | Both | [`systems/configuration-management`](../systems/configuration-management) |
| Design reviews | Preliminary Design Review, Critical Design Review, and Test Readiness Reviews | L | Both | [`systems/configuration-management`](../systems/configuration-management) |
