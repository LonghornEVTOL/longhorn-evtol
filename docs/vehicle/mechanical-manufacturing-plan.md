# Mechanical and Manufacturing Plan

**Status:** Working plan. It expands the "Mechanical, materials, and manufacturing" section of the [README](../../README.md) into the concrete rules, load cases, and gates the mechanical and manufacturing teams work to.

This document is the single place the four mechanical design rules, the numbered load cases, the fabrication release gate, and the manufacturing-control requirements live. The [vehicle baseline](baseline.md) holds the numbers; this plan holds the way we arrive at and prove them.

Last updated: 14 September 2026

---

## 1. Mechanical design rules

Four rules apply to every mechanical part. **No part is released for fabrication until it satisfies all four.**

1. **Mass gate.** The 254 lb Part 103 empty-weight limit is binding, and the parachute-counted margin is only about 4 lb ([baseline](baseline.md#8-mass-budget-option-b) §8). No part is released for fabrication until its *measured* mass replaces its estimate and the margin to 254 lb is re-checked. Mechanical mass is the largest soft number in the budget, so this rule carries the most risk.
2. **Minimum rotor clearance.** The as-deflected rotor clearance case (§2) must retain a stated minimum tip gap for every single-rotor and coaxial pair at the worst-case load combination.
3. **Continuous occupant load path.** Pilot, seat, and harness loads follow a continuous structural path: seat → center cage → landing gear. Occupant loads must never pass through the parachute mount, battery structure, or propulsion mounts.
4. **Battery retention under crash load.** Each pack is held against the defined crash load case by its own retention, independent of the fire barrier and of the wiring supports.

---

## 2. Load cases

Loads are defined as **numbered cases with values and safety factors**, not by name. Named cases invite disagreement; numbered cases are testable and set the acceptance criteria. Values are filled in at the Preliminary Design Review and frozen there; changes after PDR go through configuration management.

| ID | Case | Description | Safety factor |
| --- | --- | --- | --- |
| LC-01 | Symmetric maneuver | 2.0 g at max takeoff weight | TBD |
| LC-02 | Maximum thrust | Full-thrust burst, arm-root torsion and motor-mount load | TBD |
| LC-03 | Single motor out | Thrust redistribution held 120 s | TBD |
| LC-04 | Single pack out | Two motors lost on opposite arms, remaining six at increased load | TBD |
| LC-05 | Hard landing | Touchdown at the defined sink rate, gear and cage | TBD |
| LC-06 | Parachute deployment | Snatch load at the hardpoints | TBD |
| LC-07 | Tether | Tether tension at the vehicle anchor | TBD |
| LC-08 | Ground and transport | Cradle reactions, tie-down loads, accidental drop, parked wind | TBD |

**Load combinations.** Cases are combined per the structural verification work package; combinations and their acceptance criteria are documented before testing.

**Transport dominates joint wear.** For a folding-arm vehicle, the fold/transport cycle — not flight — is expected to be the highest-cycle-count loading on the arm joints. LC-08 therefore carries its own fatigue budget alongside the flight cases.

---

## 3. As-deflected rotor clearance case

Rotor clearance is not a nominal-gap check. The as-deflected clearance case combines, at the minimum rotor gap:

- blade flex at max thrust,
- arm bending deflection at max thrust,
- joint free play,
- hinge wear allowance,
- thermal growth.

The result must retain a stated minimum tip gap for both single-rotor and coaxial pairs. This work is shared with the coaxial spacing study (rotor gap vs efficiency on the thrust stand).

---

## 4. Battery bay specification

Each of the four bays provides retention, electrical isolation, cooling, and a **measurable fire barrier**:

- The barrier contains and routes a single-cell thermal-runaway event for a stated duration.
- The vent path is directed away from the occupant.
- There is an isolation gap to the center cage.

The fire-barrier panel test (§6) closes the performance target.

---

## 5. Fabrication release gate

A part may be released for fabrication only when all of the following are true:

1. Fixtures, gauges, and the inspection method exist and are validated.
2. Required tooling and structural test rigs are available.
3. The first article of each critical part (arm clamp, motor mount, fold joint) passes a dimensional **first-article inspection (FAI)** before the rest of the batch runs.

Tooling, consumables, test specimens, and fabrication rework are carried in the budget.

---

## 6. Verification

Verification progresses from **material specimens → joints → complete arm assemblies → integrated airframe**. Each test has documented loads, instrumentation, and pass/fail criteria established before testing.

**Instrumentation is defined before the test, not after.** Each structural test specifies sensor type, location, range, and sample rate up front — strain gauges on arm roots, accelerometers on the cage, a load cell at the parachute hardpoint — so post-test anomalies are diagnosable.

**Traceability.** Each structural test article is tied to the exact material batch, fabrication record, and drawing revision of the flight part it validates, so the qualification evidence is usable in a design review.

**Process links allowables to parts.** Production composite parts must be made by the same documented process (layup, resin, cure schedule, vacuum) that produced the qualified coupons; otherwise the allowables do not validate the flight part.

### Test articles in this plan

| Test | Closes |
| --- | --- |
| Rotor clearance envelope | Rule 2, §3 |
| Fold-cycle fatigue rig | LC-08 joint wear budget |
| Bonded-insert qualification (hot/wet) | Composite arm attachment allowables |
| Fire-barrier panel test | §4 specification |
| Weld distortion control plan | Cage interface geometry |
| FAI and gauge plan | §5 fabrication release gate |
| Instrumented structural tests | LC-01 through LC-07 |

---

## 7. Manufacturing control

### Welded 4130 cage

Distortion is controlled by a **weld distortion control plan**: weld sequence and tack plan, fixturing to hold joint geometry, a post-weld straightness check, and a defined rework-or-scrap tolerance band. Weld distortion, not strength, usually drives welded-tube-frame interface geometry.

### Composite arms

Bonded inserts are qualified by pull-out and torque-out testing under hot/wet conditioning, since arm attachment points are the most failure-prone feature of a composite arm.

### Repair and rework

A written repair and rework specification distinguishes what is repairable (and how) from what is retire-only. Welded steel repairs follow a defined weld-repair procedure; bonded composite repairs follow a written method or the part is scrapped.

### Records

Track drawing revisions, material batches, fabrication steps, inspections, fastener torque, repairs, and final measured mass for critical assemblies.

---

## 8. Open decisions

Resolve and record the following before the relevant design is released for fabrication:

- The numbered load cases and their safety factors (flight, ground, transport, parachute).
- The fire-barrier and vent-path performance target for the battery bays.
- The minimum rotor tip gap for the as-deflected clearance case.
- Whether folding arms are necessary for the first demonstrator.
- Which structural components will be purchased or manufactured in-house.
- The full-scale equipped pilot mass range and allowable center-of-gravity envelope.
- Landing conditions and energy absorption requirements.
- Acceptable arm-joint deflection, free play, and wear.
- Mass allowances for assemblies with the greatest uncertainty.

---

## 9. Deliverables

| Work package | Required output |
| --- | --- |
| Airframe | CAD assembly, fabrication drawings, load paths, and detailed mass budget |
| Folding joints | Prototype, strength and stiffness results, wear testing, and lock inspection criteria |
| Materials | Material specifications, selection rationale, and representative test results |
| Manufacturing | Tooling plan, fabrication instructions, build records, and inspection criteria |
| Tooling and inspection plan | Fixtures, gauges, first-article inspection method, and acceptance criteria per critical part |
| Repair and rework specification | Repairable vs retire-only parts, weld-repair procedure, and composite repair method |
| Structural verification | Load cases, calculations, simulation results, and comparison with physical tests |
| Integration | Assembly procedure, measured mass, center-of-gravity report, and clearance checks |
| Maintenance | Inspection checklist, damage assessment criteria, and component service records |

Each work package has a named owner, reviewer, dependencies, and completion criteria.
