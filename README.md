# Longhorn eVTOL

A student organization at The University of Texas at Austin that designs, builds, and tests an electric vertical takeoff and landing (eVTOL) aircraft.

Our goal is a **single-occupant, seated multirotor** ("the Vehicle") capable of safe, low-altitude piloted flight.

**Website:** [longhorn-evtol.vercel.app](https://longhorn-evtol.vercel.app) · **[Teams and projects](docs/teams.md)** · **[1/3-scale design](docs/vehicle/subscale-demonstrator.md)** · **[Safety requirements](docs/safety/vehicle-safety-requirements.md)**

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

First club power board, the per-pack switch: [`docs/hardware/pack-switch-board.md`](docs/hardware/pack-switch-board.md).

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

## Mechanical, materials, and manufacturing

The preliminary structure uses a **welded 4130 steel center cage, carbon-fiber folding arms, coaxial motor mounts, and landing skids**. Material selections, dimensions, and structural mass estimates remain provisional until supported by analysis and representative testing.

### Mechanical design

- **Loads and strength:** Define flight, maximum-thrust, motor-out, pack-out, landing, tether, transport, and parachute-deployment loads. Document load combinations, safety factors, and acceptance criteria.
- **Folding arms and joints:** Verify hinge, pin, clamp, and locking-mechanism strength, stiffness, wear, and resistance to accidental unlocking. Arm-lock sensors confirm engagement; the mechanical lock carries the load.
- **Rotor clearance and vibration:** Account for blade flex, arm deflection, joint play, and manufacturing tolerances. Compare structural vibration modes with rotor operating speeds and blade-passing frequencies.
- **Landing gear and occupant protection:** Define touchdown conditions, energy absorption, seat and harness load paths, equipped pilot mass limits, center-of-gravity limits, and emergency exit clearance.
- **Battery and recovery integration:** Provide battery retention, electrical isolation, cooling, and defined fire-barrier performance. Size parachute attachments for deployment loads and maintain deployment-path clearance.

### Materials and fabrication

- **4130 cage:** Specify tube dimensions, material condition, properties applicable after welding, joint preparation, welding procedures, distortion limits, inspection criteria, and corrosion protection.
- **Carbon-fiber arms:** Specify laminate construction, resin system, strength and stiffness data, environmental limits, and clamp or insert details. Evaluate local crushing, joint slip, and damage at attachment points.
- **Material qualification:** Use representative weld and composite specimens, followed by joint and assembly tests. Specimens must reflect the materials and fabrication processes used in the vehicle.
- **Manufacturing records:** Track drawing revisions, material batches, fabrication steps, inspections, fastener torque, repairs, and final measured mass for critical assemblies.

### Verification and mass control

Verification progresses from **material specimens → joints → complete arm assemblies → integrated airframe**. Each test has documented loads, instrumentation, and pass/fail criteria established before testing.

Maintain an assembly-level mass budget that includes joints, fasteners, adhesives, coatings, battery retention, and wiring supports. Replace estimates with measured masses as parts are built.

The subscale demonstrator informs architecture, integration, and test procedures. Full-scale structural strength, fatigue life, vibration behavior, and landing performance require separate analysis and verification.

---

### Build-versus-buy and tooling

Evaluate purchased and team-manufactured options for carbon tubes, folding joints, motor mounts, seats, and landing gear. Compare complete assembly mass, cost, lead time, available equipment, inspection needs, and qualification effort.

Identify required welding fixtures, machining fixtures, composite tooling, inspection gauges, and structural test rigs before releasing parts for fabrication. Include tooling, consumables, test specimens, and fabrication rework in the budget.

### Mechanical interfaces

Maintain interface drawings for motor mounts, arm attachments, battery bays, seat and harness mounts, avionics mounts, and parachute attachments. Drawings define:

- Mounting geometry, tolerances, and alignment.
- Loads transferred between assemblies.
- Installation, inspection, and removal clearances.
- Cable routing, strain relief, and clearance through folding joints.
- An owner responsible for coordinating interface changes.

### Inspection and service life

Define preflight, postflight, and periodic inspections for structural joints, arm locks, fasteners, composite parts, and landing gear.

Record flight hours, folding cycles, damage, repairs, and component replacements for critical assemblies. Establish inspection and return-to-service criteria following hard landings, rotor strikes, or transport damage. Inspection intervals and retirement limits must be supported by supplier guidance, engineering analysis, or test evidence.

### Engineering deliverables

| Work package | Required output |
| --- | --- |
| Airframe | CAD assembly, fabrication drawings, load paths, and detailed mass budget |
| Folding joints | Prototype, strength and stiffness results, wear testing, and lock inspection criteria |
| Materials | Material specifications, selection rationale, and representative test results |
| Manufacturing | Tooling plan, fabrication instructions, build records, and inspection criteria |
| Structural verification | Load cases, calculations, simulation results, and comparison with physical tests |
| Integration | Assembly procedure, measured mass, center-of-gravity report, and clearance checks |
| Maintenance | Inspection checklist, damage assessment criteria, and component service records |

Each work package has a named owner, reviewer, dependencies, and completion criteria.

### Open mechanical decisions

Resolve and record the following before the relevant design is released for fabrication:

- Whether folding arms are necessary for the first demonstrator.
- Which structural components will be purchased or manufactured in-house.
- The full-scale equipped pilot mass range and allowable center-of-gravity envelope.
- Landing conditions and energy absorption requirements.
- Acceptable arm-joint deflection, free play, and wear.
- Mass allowances for assemblies with the greatest uncertainty.

---

## Safety

Putting a person on a multirotor deserves more scrutiny than a normal project, so safety is designed in. Full list: [`docs/safety/vehicle-safety-requirements.md`](docs/safety/vehicle-safety-requirements.md).

- **Phased development.** Subscale unmanned, then full-scale unmanned and tethered, then piloted, each gated on a design review and hazard analysis.
- **Custom hardware earns its way in.** Custom PCBs and programmable logic in a flight-critical path need a design review, current-limited bring-up, and qualification at or above expected operating conditions. Nothing custom flies piloted until it has documented run time on the unmanned article.
- **Conditions before piloted flight.** Verified margins, demonstrated motor-out response, a tested E-stop and recovery parachute, completed unmanned tethered and free-flight campaigns, and a confirmed FAA basis.
- **Flight-critical compute architecture.**
  - Stabilization runs on a dedicated real-time controller, never on a device running a general-purpose OS.
  - The safety monitor lives on physically separate hardware with its own power and reset domain.
  - Companion computing is advisory only.
  - Performance claims must be measured end-to-end and backed by test data.

---

## Teams and projects

**Every team, sub-team, and project, with sizes and folders: [`docs/teams.md`](docs/teams.md).**

| Team | Sub-teams |
| --- | --- |
| [Mechanical Design](docs/teams.md#mechanical-design) | Airframe and Chassis · Seating, Ergonomics, and Cockpit · Propulsion and Duct · Landing Gear and Suspension |
| [Electrical and Power](docs/teams.md#electrical-and-power) | Power Distribution and Wiring Harness · ESC and Motor Integration · Avionics and Power Architecture · Safety Interlocks and E-Stop · Custom PCB and Hardware Design |
| [Software and Avionics](docs/teams.md#software-and-avionics) | Flight Control · Sensor Fusion and Telemetry · Cockpit Interface and Ground Station · Fault Management and Failsafes · Hardware Acceleration and FPGA |
| [Manufacturing and Operations](docs/teams.md#manufacturing-and-operations) | Welding and Metal Fabrication · Composites and Layup · Machining and CNC · Assembly and Quality Control |
| [Flight Test and Range Operations](docs/teams.md#flight-test-and-range-operations) | Test Planning · Range and Logistics · Data and Instrumentation |
| [Systems Engineering and Integration](docs/teams.md#systems-engineering-and-integration) | Requirements and Interfaces · Configuration Management |

Current projects range from a 6S buck converter and a thrust stand data logger to the [pack switch board](docs/hardware/pack-switch-board.md), the club flight controller, the FPGA safety monitor, and visual-inertial odometry. Boards are designed in KiCad and hand-assembled ([PCB design guide](docs/hardware/pcb-design-guide.md), [avionics parts](docs/vehicle/avionics-parts.md)).

---

## Repository layout

```
docs/              Teams and projects, vehicle baseline, subscale design, parts, PCB guide, safety requirements
hardware-lib/      Shared KiCad symbols, footprints, and 3D models
manufacturing/     Welding and fab, composites, machining and CNC, assembly and QC
mechanical/        Airframe, cockpit and ergonomics, propulsion and duct, landing gear
electrical/        PDU and harness, ESC and motors, power architecture, safety interlocks, power PCBs
avionics-pcb/      Flight controller, sensor nodes, safety monitor board, companion carrier, cockpit boards
software/          Flight control, perception and cameras, DroneCAN firmware, ground station, simulator
fpga/              Safety monitor, companion computing, HIL rig
flight-test/       Test plans, range logistics, flight data
systems/           Requirements, interfaces, configuration management
```

Most folders are empty placeholders and will fill in as each team starts work.
