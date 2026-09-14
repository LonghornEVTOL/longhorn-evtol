# Longhorn eVTOL

A student organization at The University of Texas at Austin that designs, builds, and tests an electric vertical takeoff and landing (eVTOL) aircraft.

Our goal is a **single-occupant, seated multirotor** ("the Vehicle") capable of safe, low-altitude piloted flight.

> **Status:** Founding. Constitution drafted; registration with the Dean of Students is pending a University Advisor.

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

A first-pass sizing, frozen at the Preliminary Design Review. Full derivation, requirements, and mass budget: [`docs/vehicle/baseline.md`](docs/vehicle/baseline.md).

| | Baseline v0 (preliminary) |
| --- | --- |
| Configuration | Coaxial octocopter (X8): 4 arms, 8 counter-rotating rotors |
| Propellers | 40 in fixed-pitch carbon fiber |
| Empty weight | ≈ 245 lb estimated against the 254 lb limit (target ≤ 229 lb) |
| Gross mass | ≈ 454 lb with a 209 lb pilot |
| Thrust-to-weight | ≥ 2.0 all motors · ≥ 1.7 one motor out · ≥ 1.45 one pack out |
| Motors | 24S heavy-lift outrunners, ≈ 40 to 60 Kv, ≥ 51 kgf peak thrust each |
| ESCs | ≥ 120 V, ≥ 120 A continuous, FOC, DroneCAN telemetry |
| Battery | 4 independent 24S5P Li-ion packs (21700 high-power cells), ≈ 7.8 kWh total, 86 V nominal |
| Hover power | ≈ 55 to 65 kW, ≈ 5 to 6 min to 20% state of charge |
| HV safety | Per-pack contactor, pre-charge, and fuse; hardwired E-stop; insulation monitoring |
| Flight control | STM32H7-class RTOS controller, triple IMU, DroneCAN motor bus |
| Safety monitor | Independent iCE40-class FPGA with its own power and reset, can cut power and fire the parachute |
| Recovery | Ballistic parachute sized for gross mass |
| Subscale demonstrator | ≈ 1/3 scale coaxial X8, 15 in props, 6S LiPo, ≈ 5 kg |

**The mass budget does not close with margin yet.** Finding about 16 lb is the main design problem for year one.

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

Year one work, sized from a weekend to a full semester. Everything here feeds the subscale demonstrator, the thrust stand, or the full-scale design.

**Electrical and Power**
| Project | What it involves |
| --- | --- |
| Buck converter board | Design, lay out, and bring up a 6S (25 V) to 5 V, 3 A converter that powers the subscale avionics |
| Current and voltage sense board | Shunt plus current-monitor IC board that logs motor current on the thrust stand |
| Subscale power distribution board | High-current PDB with fusing and a voltage tap for the 6S demonstrator |
| Battery pack monitor | Cell voltage and temperature logging for the pack log required by Article X |
| Pre-charge and contactor driver | HV bus pre-charge, contactor coil driver, and interlock for the full-scale packs |
| Hardwired E-stop loop | Contactor interlock chain that opens every pack with no software in the path |

**Software and Avionics**
| Project | What it involves |
| --- | --- |
| Thrust stand data logger | Python tool that records load cell, RPM, current, and voltage and plots thrust and efficiency curves |
| Ground station dashboard | Live MAVLink telemetry display for the subscale demonstrator |
| Single-axis PID rig | Tune attitude control on a one-degree-of-freedom test bench |
| Coaxial X8 simulation | Software-in-the-loop model of the vehicle, including motor-out and pack-out cases |
| Failsafe logic | Geofence, loss of link, and motor-out compensation on the flight controller |

**Hardware Acceleration and FPGA**
| Project | What it involves |
| --- | --- |
| Heartbeat watchdog | First RTL on a small FPGA dev board: watch a heartbeat signal and trip an output when it stops |
| Safety monitor RTL | Heartbeat, attitude, rate, and power limit checks with authority to cut power, plus testbenches |
| Hardware-in-the-loop rig | Run flight software and the safety monitor against a simulated vehicle |

**Mechanical Design and Manufacturing**
| Project | What it involves |
| --- | --- |
| Motor mount bracket | CAD, hand calcs, FEA, then machine it at Texas Inventionworks |
| Thrust stand frame | Rigid stand with a load cell rated past 60 kgf for full-scale motor testing |
| Subscale airframe | Design and build the 1/3 scale coaxial X8 frame |
| Composite arm coupons | Lay up carbon tube samples and test them to failure to set arm design allowables |
| Landing skid drop test | Size skids for hard landing loads and verify with a drop rig |
| Weld coupons | Practice and test tube joints before any airframe welding |

**Flight Test, Systems, and Outreach**
| Project | What it involves |
| --- | --- |
| Motor and propeller characterization | Written test procedure, then thrust stand runs that replace the catalog numbers in the baseline |
| Mass budget tracker | Keep the 254 lb budget current as parts are weighed and selected |
| Interface control documents | Define power, data, and mechanical interfaces between teams |
| Test site survey | Find and document off-campus sites for subscale and tethered testing |
| Sponsor packet | One-page and deck versions of the program for sponsors |

---

## Membership

- Open to UT Austin students, faculty, and staff. **No prior experience or specific major required.**
- **General Members** join by showing up. No application, no vote.
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
docs/              Vehicle baseline, constitution, design reviews, safety, regulatory, meeting notes
manufacturing/     Welding and fab, composites, machining and CNC, assembly and QC
mechanical/        Airframe, cockpit and ergonomics, propulsion and duct, landing gear
electrical/        PDU and harness, ESC and motors, power architecture, safety interlocks, PCBs
software/          Flight control, sensor fusion and telemetry, ground station, fault management
fpga/              Safety monitor, companion computing, HIL rig
flight-test/       Test plans, range logistics, flight data
systems/           Requirements, interfaces, configuration management
outreach/          Sponsorship, recruitment, media
```

Most folders are empty placeholders and will fill in as each team starts work.
