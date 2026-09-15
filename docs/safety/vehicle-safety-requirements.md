# Vehicle Safety Requirements

The engineering and test requirements every Longhorn eVTOL vehicle, board, and test follows. They apply to the subscale demonstrator, the full-scale unmanned article, and the piloted vehicle. No schedule pressure or design trade waives them.

---

## S1. Development phases

1. Development proceeds in three phases: **subscale unmanned demonstrators**, then a **full-scale unmanned and tethered test article**, then a **piloted vehicle**.
2. Work in a phase starts only after a completed design review and a current hazard analysis for that phase, approved in writing.

## S2. Custom hardware qualification

1. Before any powered test of a new configuration: a Preliminary Design Review, a Critical Design Review, and a written hazard analysis listing credible failure modes, their consequences, and mitigations.
2. Structural, thermal, and electrical margins are documented by analysis and, where practicable, verified by test.
3. Any custom PCB, programmable logic configuration, or other custom electronics in a flight-critical path (power distribution, motor control, sensing, safety interlocks, recovery system triggering) additionally requires:
   - a documented design review,
   - bench bring-up under current limit,
   - a qualification test at or above expected operating current, voltage, temperature, and vibration,
   - before installation in any vehicle.
4. Programmable logic in a flight-critical path is also exercised on the hardware-in-the-loop rig, with testbench and results recorded.
5. No custom board or programmable logic flies on a piloted vehicle until it has documented run time on the unmanned test article.

## S3. Test readiness and procedures

1. Every powered test is preceded by a signed Test Readiness Review.
2. Every powered test runs under a written procedure specifying: configuration, objectives, abort criteria, personnel and stations, exclusion zone, and emergency response plan.
3. Full-scale powered testing progresses from restrained, to tethered, to free unmanned flight, and only then to piloted flight under S4.

## S4. Conditions before any piloted flight

No flight with a person aboard, tethered or free, until all of the following are satisfied and documented:

1. A Critical Design Review and current hazard analysis for the exact flight configuration are complete and approved.
2. Structural and propulsion margins for the occupied configuration, including the factor of safety and the response to a single propulsion or power failure, are established by analysis and verified by test.
3. The full-scale test article has completed a tethered campaign and a free unmanned flight campaign with no unresolved anomaly.
4. The hardwired emergency stop, the emergency power disconnect, and the recovery parachute are functionally tested in the flight configuration, and failsafe and **motor-out** logic has been demonstrated in flight on the unmanned article.
5. The regulatory basis for the flight is identified in writing, the vehicle and operation conform to it, and any required FAA notification, authorization, or exemption is obtained.
6. The pilot has completed a documented training and currency program and has been briefed on the hazard analysis and emergency procedures.
7. The test site is appropriate, site permission is in writing, and the flight is not over people.
8. Written approval for the flight from the responsible engineering, safety, and faculty reviewers.

## S5. Test operations rules

Always, at every test:

1. No person within the plane of a rotor while a propulsion system is energized, except a seated pilot in a configuration approved under S4.
2. Propellers removed, or motor power physically disconnected, for any bench work on an energized flight controller.
3. A minimum standoff distance, set in the test procedure, for everyone not assigned a station inside the exclusion zone.
4. No flight over people, over moving vehicles, or beyond visual line of sight.
5. A designated safety observer with immediate communication to the operator at every powered test.
6. Appropriate personal protective equipment in fabrication and test areas.
7. No one works alone on a hazardous operation: high-voltage work, battery charging, machining, or composite layup.

## S6. Batteries

1. Lithium packs are charged, stored, and transported only in approved locations, with fire suppression available.
2. Damaged, swollen, or over-discharged cells are removed from service immediately and disposed of properly.
3. Every pack in service has a log: cycles, capacity checks, and incidents.

## S7. Flight-critical computing architecture

For any vehicle intended to carry a person:

1. Stabilization and motor command run on a dedicated real-time controller, never on a device concurrently running a general-purpose operating system, and never dependent on one staying up.
2. The independent safety monitor is on a physically separate device from the flight controller, with its own power source or separately protected rail and its own reset domain, so a fault, brownout, or reset in one cannot disable the other. Its logic is simple enough to review in full at a design review.
3. Companion computing (perception, mapping, logging, telemetry) is advisory only: no direct authority over motor command, and its loss does not degrade stabilization or the safety monitor.
4. Any application-specific integrated circuit (ASIC) developed by the team flies only on unmanned test articles or in non-flight-critical roles, never in the stabilization, motor command, safety monitor, or recovery path of a piloted vehicle.
5. Control loop performance claims are stated as measured end-to-end latency from sensor sample to motor response, backed by test data, not by the processing time of any single stage.

## Incidents

Any injury, fire, uncommanded flight, loss of control, or property damage suspends operation of the affected system until it is reviewed and the hazard analysis is updated.
