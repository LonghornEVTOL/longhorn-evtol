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
docs/              Constitution, design reviews, safety procedures, regulatory, meeting notes
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
