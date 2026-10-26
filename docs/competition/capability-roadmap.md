# Capability roadmap: the constants, on a timeline to 2028

**Status:** v1. This is the sequencing document. It takes the five permanent capabilities from [rules watch](rules-watch.md) and puts them on the phase structure in the [program plan](suas-program-plan.md), front-loading the three that need flight hours.

**The premise.** The 2027 and 2028 handbooks are unpublished, and the tasks our airframe is shaped around are the newest rules in the book. But five capabilities have been scored in **every cycle from 2014 to 2026**. Building those is never wasted work, and they are what this roadmap sequences.

---

## 1. The five constants, and why they sequence in this order

| # | Capability | Cycles | Needs flight hours? | Owner |
| --- | --- | ---: | --- | --- |
| **1** | **Autonomous waypoint navigation** | 12/12 | **Yes — the most** | Flight software |
| **2** | **Aerial detection and geolocation** | 12/12 | **Yes** | Vision |
| **3** | **Payload air delivery** | 12/12 | **Yes, but mostly not on the aircraft** | Payload |
| 4 | Documentation | 12/12 | No | Everyone, continuously |
| 5 | Safety and airworthiness compliance | 12/12 | No — it is a gate | Safety |

**The first three are sequenced first because they are the only ones that need airframe time, and airframe time is the scarcest resource in the program.** Documentation and compliance are continuous and parallel — documentation is a byproduct of the other four if we set it up that way, and compliance is a paperwork and discipline problem that can be solved before anything flies.

**Three structural facts drive the ordering:**

**Navigation gates everything.** The rules require a complete autonomous lap *before* any delivery is attempted, and deliveries attempted before a lap score zero. Mapping and search both happen inside an autonomous mission. **So navigation is not one of three parallel workstreams — it is the dependency for the other two.**

**Delivery and detection can be developed off the competition aircraft.** The drop mechanism and its physics characterise on a stairwell and a hovering multirotor; detection trains on datasets and a mannequin in a field. **Neither needs the quadplane to exist.** That is what makes it possible to front-load all three at once.

**And the test ladder, not the calendar, sets the pace.** The ten gates in [test and verification](test-and-verification.md) are sequential by construction — no gate is entered until the previous one's exit criteria are met and signed. The phases below are hung on those gates.

---

## 2. The timeline

Phases match the [program plan](suas-program-plan.md). Dates are the gates, not the work.

### Phase 0 — Foundations · now to Feb 2027

**Everything here is free or nearly so, and none of it needs an aircraft.**

| Capability | Work | Done when |
| --- | --- | --- |
| **Navigation** | SITL running with a custom aero file and real `SIM_FRM_*` values. The competition mission, fence and failsafes as an autotest case in CI. **Waypoint closest-approach check written as a log post-processor** | Full mission flies unattended in SITL; every injected failure ends safely (**gate G2**) |
| **Detection** | Email the Archangel authors. Pretrain on HERIDAL, Archangel, NOMAD, SARD. Compute the pixel-size distribution our camera will produce at 150–250 ft | A baseline model exists and its training distribution is scale-matched |
| **Delivery** | Over-centre sear latch in metal, cycled 500 times on the bench. **Tier-2 drop rig** — stairwell or garage, surveyed grid, instrumented dummy — and 30 drops per candidate canopy | `v_d` and `k` **measured**, not assumed. Latch proven fail-safe retained |
| **Compliance** | TRUST for everyone. 2–4 people through Part 107. Registration lane decided with EHS. **UT flight-operations package submitted** | Cleared to fly, with two weeks of lead time respected |
| **Documentation** | Decision-record template and the five-heading test template live in the repo, rendering on the site. CI accessibility gate | Every test since G0 is already published |

**Phase 0 exit: Conceptual Design Review, Feb 2027.** Configuration frozen. Releases procurement wave 2.

### Phase 1 — Trainer flying · Feb to Jun 2027

The trainer quadplane is the airframe for all three flight capabilities before the competition aircraft exists.

| Capability | Work | Done when |
| --- | --- | --- |
| **Navigation** | Gates G0 through G6 on the trainer: bench, thrust stand, sim-on-hardware, 1-DOF rig, tethered hover, free hover with the tune. **Then G7 fixed wing and G8 transition at altitude** | Hands-off level flight at each transition increment; clean back-transition |
| **Navigation** | **First autonomous lap.** `WP_RADIUS` under 15.24 m, `WP_MAX_RADIUS` as a guard, lead-in and lead-out waypoints, `NAVL1_*` tuned against a 45.7 m turn radius | **Measured closest approach to every waypoint, from the log, under 30.5 m** (gate G9) |
| **Detection** | **One deliberate data sortie** — real mannequin and pop-up tent, 150/200/250 ft at our actual camera pitch, several sun angles, all poses including face-down under a bush, with confuser objects. A few hundred frames | A measured detection rate on prone and occluded targets |
| **Delivery** | **Tier-3 drops from a hovering multirotor at 45.7 m.** Ground anemometer feeding the release-point solver | Full-system `v_d` and `k`; wake-fouling question answered |
| **Documentation** | Everything above published as it happens | The website is already the development record |

**Phase 1 exit: Preliminary Design Review, Apr 2027**, then the trainer milestones through June.

### Phase 2 — Integration summer · Jun to Sep 2027

Three capabilities meet on one airframe for the first time.

- **Detection onto the trainer**, geolocating real targets in flight. **Measure image-to-ground error in metres** — [perception](subsystems/perception.md) flags this as the open term that [payload delivery](subsystems/payload-delivery.md) currently assumes at 2 m.
- **Delivery onto the trainer**, with the release-point solver closing on a real target. **30 full-system drops**, because 9 of 10 tells you nothing (the 95% interval on 9/10 is 60% to 98%).
- **Mapping** attempted for the first time, against a runtime budget inside the clock.
- **A full mission end to end on the trainer**: autonomous lap, mapping run, detection, two deliveries, landing, under 45 minutes **including setup**.

### Phase 3 — Competition aircraft · Sep 2027 to Feb 2028

**Attend the 2027 event, 1–4 October, as observers.** Watch the inspection table, time the setups, watch the crashes, talk to teams.

Then the competition aircraft repeats the entire gate ladder from G0. **Nothing carries over except the software, the parameters and the knowledge** — the airframe is new and gets no credit for the trainer's hours. First hover November, first transition December, Critical Design Review once the 2028 rules publish.

### Phase 4 — Proving · Feb to Jun 2028

- All three capabilities on the competition aircraft, in competition configuration, at competition weight.
- **Dress rehearsal, May**: full mission on the clock, with the toolless assembly timed and the deliverable handover rehearsed.
- Register by approximately 1 April. **Qualification videos submitted early** — flight order follows the order videos are accepted, and an earlier slot means cooler, calmer air.

### Phase 5 — Submit and compete · Jun to Sep 2028

TDR and website submitted; both drawn from a corpus two years deep rather than written in July. Compete.

---

## 3. The three constants, each in one page

### Navigation — the dependency

**Target:** autonomous takeoff, laps hit within 100 ft in order, autonomous landing, inside a 150 ft floor and a 45.7 m turn radius.

**What is settled.** Buy the flight controller — no innovation category exists, so a custom one scores zero. ArduPilot QuadPlane. **AUTO mission upload with delivery waypoints spliced in, not GUIDED setpoint streaming** — the mission survives a companion crash, a reboot and link loss. `MAV_CMD_DO_REPOSITION` or `MISSION_ITEM_INT` with `current = 2` for position commands, never `SET_POSITION_TARGET_GLOBAL_INT`, which hardcodes latitude and longitude to zero on Plane.

**What is unsettled, and it is the largest gap in the program:** the transition parameter set, the VTOL takeoff and landing mission commands, TECS for a quadplane, and `NAVL1_*` against our turn radius. → [flight autonomy](subsystems/flight-autonomy.md) section 4.

**The thing most likely to bite:** the autopilot marks a waypoint complete either by an acceptance distance that *grows with turn angle*, or by crossing a perpendicular finish line — and the line-crossing path fires however far away we were. **"Reached waypoint" in the console is not evidence of scoring compliance.** → [flight autonomy](subsystems/flight-autonomy.md) section 1.

### Detection and geolocation

**Target:** find a mannequin and a tent from aerial imagery, autonomously, and produce coordinates good enough to deliver within 50 ft.

**What is settled.** Small model at high resolution, not a large model at 640. Copy-paste onto our own aerial backgrounds at a monotone-scale-matched pixel-size distribution, with randomised lighting and texture and deliberately synthesised occlusion. **No harmonisation** — Poisson blending scored 7.5 AP below doing nothing. **Build no simulator** — synthetic-only aerial detection is about 10 mAP, and doubling render fidelity bought 0.1 mAP while geometry alignment bought 7.4.

**The thing most likely to bite:** COCO is 84.5% standing people and the handbook puts the mannequin in any orientation. **Test a face-down mannequin half under a bush at 200 ft before building anything around the model.** → [perception](subsystems/perception.md).

### Payload delivery

**Target:** both objects carried simultaneously, retardant required, released at the 150 ft floor, within 50 ft.

**What is settled.** Cruciform canopy sized for 12 m/s — **slower loses**, because drift scales as altitude over descent rate and a 5 m/s canopy scores 18.7% against 85% for 12 m/s. Packed but unbagged with a 1.0–1.5 s opening delay. **Two independent servo-tripped over-centre sear latches** with `SERVOn_TRIM` at the grab position, because one unintentional departure costs 100 points — as much as a perfect delivery. Slow translating release at 5–8 m/s at exactly the floor. **A ground anemometer, not RTK.**

**The thing most likely to bite:** the judge, not the physics. A 255 g bottle arriving at 39 ft/s under a 9-inch canopy may be called freefall with a handkerchief. **Ask the organisers in writing what qualifies as a retardant, and bring an 8 m/s canopy as the defensive option.** → [payload delivery](subsystems/payload-delivery.md) section 10.

---

## 4. The strategic check, and the one thing that could invalidate this

The published 2026 scoresheet says **400 of 1000 mission points need no successful flight at all**, that fewer than a third of scoring teams put anything on the board for delivery or mapping, and that **the modal team flew one lap — worth two points.** A crash costs 500.

**So this roadmap deliberately does not chase ten laps.** It builds the three flight constants to a conservative, repeatable standard, banks the 650 points that do not require flying well, and treats finishing intact as the primary objective. A team that sets up fast, passes inspection, flies three or four clean laps and lands beats most of the 2026 field.

**And the honest risk:** the 400 points this architecture is optimised for sit on **Design for Rapid Response, which has existed for exactly two cycles.** It is the newest rule in the handbook and the most likely to change.

**Which is the whole argument for this document.** The five constants are 12-for-12. If Rapid Response is re-tuned, re-weighted or removed in the 2027 handbook, every hour spent on navigation, detection, delivery, documentation and compliance still counts. **The hedge is not a different aircraft. It is a work order that front-loads the things that cannot be deleted.**

When the 2027 handbook publishes, [rules watch](rules-watch.md) is what gets re-run against it — not this.
