# SUAS competition program

Target: **RoboNation SUAS, 2028 cycle**, Skyway Range, Tulsa. We attend the 1–4 October 2027 event as observers.

Nine documents, about 2,500 lines. Read them in this order depending on what you need.

---

## Start here

| Document | What it is |
| --- | --- |
| **[suas-2028.md](suas-2028.md)** | **The aircraft.** Why this competition, the configuration trade, the mass and time budgets, what the aircraft must do, and the budget. Start here if you want to know what we are building |
| **[suas-program-plan.md](suas-program-plan.md)** | **The program.** Decisions on record, hard dates and fees, five phases from Oct 2026 to Sept 2028 with owners and gates, seven sub-team charters, four procurement waves each gated behind a design review, and the risk register |

| **[capability-roadmap.md](capability-roadmap.md)** | **The sequencing.** The five permanent capabilities on a phase timeline to 2028, with the three that need flight hours front-loaded. Read this to know what to do first |

## Strategy and rules

| Document | What it is |
| --- | --- |
| **[rules-watch.md](rules-watch.md)** | **Where the points actually are.** The 2026 per-task scoresheet, which shows 400 of 1000 mission points need no successful flight and that the modal team flew one lap. Also which requirements have survived every rewrite since 2014, the obstacle-avoidance history, and what the two-year cycle means for 2028 |
| **[documentation-strategy.md](documentation-strategy.md)** | **250 points winnable before the aircraft flies.** The TDR rubric recovered from RoboNation's other competitions, a survey of eighteen competitor websites, and the three open goals nobody is defending |

## Compliance and process

| Document | What it is |
| --- | --- |
| **[faa-compliance.md](faa-compliance.md)** | **What we are legally allowed to do.** The two legal pathways, autonomy under Part 107, the dropping rule, registration and Remote ID, UT's own policy and its two-week lead time, and the compliance matrix as actions with owners |
| **[test-and-verification.md](test-and-verification.md)** | **How we avoid being one of the 21 crashes in 55 flights.** Ten flight-test gates with numeric exit criteria, what SITL does and does not model, the CI strategy, and the log-review checklist |

## Subsystems

| Document | What it is |
| --- | --- |
| **[subsystems/payload-delivery.md](subsystems/payload-delivery.md)** | Drop physics worked from first principles, the error budget, the release mechanism, and why slower descent loses the accuracy award |
| **[subsystems/avionics-and-datalink.md](subsystems/avionics-and-datalink.md)** | Radio architecture and link budget, the 15-second comms-loss rule, co-site interference, and companion-computer integration |
| **[subsystems/perception.md](subsystems/perception.md)** | Detection and mapping. Datasets, training strategy, and why the pose prior beats the pixel count |
| **[subsystems/flight-autonomy.md](subsystems/flight-autonomy.md)** | Navigation. How a waypoint is actually detected, and why the autopilot's "Reached waypoint" is not evidence of scoring compliance |

---

## The five findings that matter most

Each links to where it is argued in full.

**1. 400 of 1000 mission points need no successful flight, and almost nobody banks the flying points.** In 2026, Efficient Operators averaged 177.8 of 200 and every scoring team took it; Flight Endurance averaged 38.6 of 250. The modal team flew one lap, worth two points. **Bank the documentation, the operator count and the rapid-response properties, then fly a profile that finishes.** → [rules-watch](rules-watch.md)

**2. A crash costs 500 points and there were 21 of them in 55 flights.** One team banked 225 points, crashed once, and scored zero. Reliability outscores cleverness, which is why the test ladder exists. → [test-and-verification](test-and-verification.md)

**3. The competition is not run under Part 107.** The handbook requires TRUST and never says "Part 107" in 52 pages, because it presumes the §44809 educational exception. Autonomy is expressly permitted, and a visual observer alone can discharge see-and-avoid — which is both the legal architecture for an autonomous lap and the two-person crew that scores full marks. → [faa-compliance](faa-compliance.md)

**4. Slower parachutes lose.** Wind drift scales as altitude over descent rate, so a 5 m/s canopy scores 18.7% inside the 50 ft radius — worse than an illegal freefall — while 12 m/s scores 85%. A $150 ground anemometer is worth more than RTK. → [payload-delivery](subsystems/payload-delivery.md)

**5. The comms-loss rule got twice as strict and nobody announced it.** 30 seconds became 15, and 3 minutes became 90 seconds, between 2024 and 2026. Fast reconnection no longer saves you; the link must hold continuously. → [avionics-and-datalink](subsystems/avionics-and-datalink.md)

---

## Status, and what is unfinished

Every document carries its own status line and its own "Outstanding" section. Nothing here is finished research, and the gaps are named rather than papered over.

**Complete:** payload delivery · test and verification · FAA compliance · avionics and datalink.

**Partial, with the remaining work listed in each:** rules watch (the verbatim changelog corpus and the 2027 risk table) · perception (camera trade, Jetson throughput, geolocation error budget, mosaicking) · documentation strategy (confirming whether the 10-page limit excludes appendices).

**Largest remaining gap:** the ArduPilot QuadPlane transition parameter set, the VTOL takeoff and landing mission commands, TECS for a quadplane, and `NAVL1_*` tuning against our 45.7 m turn radius. Enumerated in [flight autonomy](subsystems/flight-autonomy.md) section 4, which also answers the waypoint-detection question in full.

### Conventions used throughout

- Claims are sourced. Where something could not be verified it says so, rather than being stated as fact.
- Where the evidence contradicts our plan, that is written down too — each subsystem document ends with a section arguing against its own recommendation.
- Rules are cited from the **2026** handbook unless stated. **Every point value must be re-verified against the 2028 rules when they publish**, and [rules-watch](rules-watch.md) says which are likely to move.

### Two things only a human can close

- **The UT Austin range-collapse story.** No public source exists for it. If it is real it is institutional memory we hold and the record does not — worth asking Dr. Sirohi and Texas Aerial Robotics.
- **The Archangel dataset.** 178,800 mannequin images at our exact altitude band in stand, squat and prone poses, with no public download URL. One email to the authors is the highest-value action in the perception plan.
