# SUAS Program Plan

How Longhorn eVTOL gets from today to a scored run at the RoboNation SUAS competition. Companion to the [aircraft design](suas-2028.md), which covers sizing and configuration; this document covers the schedule, the sub-team assignments, and what we buy when.

**Baseline date:** 14 October 2026. **Target entry:** the 2028 cycle, with the 2027 event attended as observers.

---

## 1. Decisions on record

| Decision | Call | Why |
| --- | --- | --- |
| Competition | **RoboNation SUAS** | Annual, stable task skeleton, drivable venue, no other UT org entering it |
| Entry year | **2028**, attend 2027 as observers | A 2027 entry needs a qualified autonomous aircraft by ~July 2027, before we have money or a flying airframe |
| Aircraft | **Lift-plus-cruise quadplane, ~15 lb** | See the [design doc](suas-2028.md). Endurance scoring is quadratic; both Embry-Riddle and UC Berkeley moved from multirotor to fixed-wing VTOL for 2026 |
| Flight controller | **Buy commercial (Pixhawk / Cube class)** | Section 3 |
| 1/3-scale coaxial X8 | **Dormant** | Not needed for SUAS; $8–12k that belongs in the competition aircraft. Its pack architecture, power boards, avionics choices and safety requirements carry over |
| Part 103 piloted vehicle | **Long-term goal, not a current build** | Keeps the club distinct from Texas Aerial Robotics and is why our advisor is a coaxial-rotor researcher |
| Operators | **Two, by design** | 200 points: `200 × min(1, (4−O)/2)` |
| Autopilot firmware | **ArduPilot (ArduPlane QuadPlane)** | Section 4 |

---

## 2. Hard dates and money

The 2027 handbook is not published yet ("Coming Soon"), so the dates below are the published 2026 pattern plus the one confirmed 2027 date. Re-baseline when the 2028 handbook appears.

**Two facts from the 2026 handbook that make the readiness video the hardest date in the season:** up to **50 teams are invited on site at all**, ordered by qualified Proof of Flight Readiness submission, with later submissions waitlisted at the organisers' discretion; and every invited team gets **up to two mission demonstrations, with the better one counting**. A backup instance of the locked design may be brought to competition and flown, one per attempt, which makes the spare airframe a scoring strategy rather than insurance. See [handbook 2026 §8](handbook-2026.md#8-two-things-that-change-the-strategy).

| Item | 2026 actual | 2027 | 2028 (assumed pattern) |
| --- | --- | --- | --- |
| Registration window | 01 Dec 2025 – 01 Apr 2026 | not published | ~Dec 2027 – Apr 2028 |
| Proof of Flight Readiness video | 01 Apr – 22 Jul 2026 | not published | ~Apr – Jul 2028 |
| Proof of Pilot Competence video | 01 Apr – 01 Sep 2026 | not published | ~Apr – Sep 2028 |
| Fee + design documentation due | 29 Jul 2026 | not published | ~Jul 2028 |
| Event | 14–17 Sep 2026, Skyway Range, Tulsa | **01–04 Oct 2027**, Skyway Range | not announced |

**Fees (2026, non-refundable):** **$1,250 registration** due ~1 April, plus **$250 competition fee** due ~29 July if approved to fly. Budget $1,500 in fees alone.

**Prize:** 1st place in mission demonstration paid **$4,000** in 2026. Separate awards exist for the technical report, the website, safety, innovation, and MathWorks simulation.

**Two scheduling details worth exploiting:**

1. **Flight order follows the order in which qualifying videos were accepted.** Submitting the flight-readiness video in June instead of late July buys an earlier slot, which in Oklahoma means calmer morning air and less exposure to afternoon weather holds.
2. **The mission clock is 45 minutes and it includes setup.** You may not power on the aircraft or GCS before it starts, judges start it when the airspace is ready whether or not you are, and it stops only when you have cleared the runway, released the airspace, and handed in deliverables. Teardown is a separate 10 minutes. Every minute of setup is a minute of lap time, which is why toolless assembly is worth more than its 25 points.

---

## 3. The flight controller: buy it

| Argument | Detail |
| --- | --- |
| No points for building one | SUAS has no innovation category in its 1250 points. DBVF had 50 points for innovation; SUAS has zero |
| The transition is the risk | ArduPilot's QuadPlane code handles the rotor-to-wing handoff. On supported hardware that is tuning; on custom hardware it is porting and re-validating the most crash-prone phase of flight |
| The rules demand proven failsafes | Autonomous RTL at 15 s lost link, autonomous flight termination at 90 s, both triggerable by pilot and GCS, verified at inspection |
| The winners buy | KFUPM ASCENT won the 2026 mission demonstration on a **Cube Orange+ running ArduPilot**. Embry-Riddle won 2025 having deliberately chosen a Pixhawk Cube so effort went to integration and operational testing |
| We get custom-FC experience elsewhere | The TAR swarm flight-controller PCB already covers that learning |

**Buy:** Holybro **Pixhawk 6X** if in stock, **6C** ($165.99) as the fallback. Two eventually — one on the trainer and test mule, one on the competition aircraft.

**Where club-designed hardware does earn points:**

| Board | Points it touches |
| --- | --- |
| Four sub-100 Wh Li-ion pack modules | **75** outright, plus the weight term |
| Pack switch and power distribution board | Weight, reliability, report credibility |
| Avionics power module (the Level 2 onboarding board) | Reliability, report |
| Payload release driver board | Up to **100** across the two deliveries |

Power boards are flight-critical: if a pack switch browns out mid-transition the aircraft falls. They get current-limited bring-up, load-bank testing, and a commercial UBEC kept as a drop-in fallback until ours has flight hours. The rule is not "custom hardware is safe, custom firmware is not" — it is **do not rewrite complex safety-validated control code; do build hardware whose risk can be retired with a multimeter and a load bank.**

---

## 4. Technical stack

Sourced where possible. Figures we could not find are marked as things to measure ourselves — and measuring them is cheap compared with assuming.

| Subsystem | Choice | Evidence |
| --- | --- | --- |
| Autopilot | **ArduPilot ArduPlane QuadPlane** | First-class quadplane mode set, mature transition tuning, used by KFUPM (Cube Orange+), Cal Poly Pomona, Virginia Tech, Cornell, TJ UAV |
| Companion | **Jetson Orin Nano Super 8 GB** | KFUPM won on a plain **Jetson Nano**; Cal Poly Pomona runs an **Orin Nano**. Compute is not the bottleneck |
| Camera | Downward-pointing gimbal camera with hardware trigger | Cal Poly Pomona uses a **SIYI A8 mini 4K gimbal pointed straight down** |
| Datalink | RFD900-class telemetry; Herelink as an upgrade | Cal Poly Pomona uses **RFD900 to Mission Planner**; KFUPM and Monash use **Herelink** |
| Detection | **YOLO (v8s/v11s/YOLO26) exported to TensorRT FP16, tiled (SAHI-style)** | Cal Poly Pomona runs **YOLO26m TensorRT with SAHI sliced inference onboard**. YOLOv8s at 640 on Orin NX 16 GB: **7.94 ms FP16** (126 fps), 5.53 ms INT8. SAHI's paper reports +5 to +7 AP on small objects |
| Mapping | **Live GPS-placed mosaic onboard as the guaranteed deliverable**, full photogrammetry only as an offline upgrade | Cal Poly Pomona does **onboard incremental ORB/RANSAC stitching** of geotagged photos. Near-real-time mosaicking is cheap: 60 frames in 26.3 s single-threaded on a laptop CPU. **ODM is not viable inside the clock** — the only measured run, 46 images at high feature quality, took **19m50s on a laptop and 2h31m on a Jetson Nano**, and its matching stage is CPU-bound with the GPU under 2% busy. [OpenREALM](https://github.com/laxnpander/OpenREALM) is the open-source starting point |
| Georeferencing | Calibrated pinhole model, autopilot attitude, flat-ground intersection, multi-frame median | Virginia Tech publishes a detection-to-lat/lon module. NTNU's vision target estimator achieved **3.4 m mean error** |
| Airdrop | **Ballistic release-point solution using the autopilot's wind estimate, with a fast retardant device, released from a hover** | NTNU's autonomous ballistic airdrop from a small fixed-wing delivered objects to a **5.5 m mean miss** — a 3× margin inside the 50 ft (15.2 m) radius. Monash drops the 155 g beacon from 45 m with a passive-braking servo release |
| RTK GNSS | **Skip for the first season**; use an L1/L5 receiver | Waypoint tolerance is 100 ft and the drop needs ~2 m, so RTK buys little. It would only matter if map accuracy is scored |
| GCS | Mission Planner or QGroundControl, plus a thin two-operator mission app | Monash built a **PyQt6 operator app over MQTT**. Inspection requires the GCS to show a map, waypoint threshold under 50 ft, speed in knots, altitude in feet, with no cloud dependency |

**What we must measure ourselves, because nobody has published it:** `--fast-orthophoto` timing on our own ground laptop with our own image count; tiled-inference throughput on our own Orin; descent rate and drift of our own drogue; and wind-compensated versus uncompensated drop error.

---

## 5. Scoring-driven requirements

### 450 points are available before the aircraft leaves the ground

| Source | Points | Where it is won |
| --- | --- | --- |
| Design for Rapid Response | 200 | **At the inspection table.** Weight `50 × min(1,(35−W)/20)`, full at ≤15 lb · packing tier 50/25/10 · toolless assembly in 3 min with ≤4 people, 25 · **every battery under 100 Wh, 75** |
| Technical Design Report | 150 | 10 pages, IEEE format. Competition / Design / Testing Strategy at 25% each |
| Team website | 100 | **40% documentation of development and testing, 40% site quality (navigation, mobile, W3C accessibility, search), 20% team info** |

The website rubric is an unusual advantage for us: we already run a Next.js site with a design system, and most engineering teams lose half of this category to a thin one-pager. Calgary placed **34th in mission demonstration and 2nd of 49 on report and website** — the documentation lane is independently winnable while we are still learning to fly.

### Penalties decide the ranking

| Event | Penalty |
| --- | --- |
| **Crash or collision** | **50% of demonstration points, each, uncapped** |
| Ignoring a judge's takeover or kill command | 50% each |
| Each item that falls off the aircraft | 10% |
| Each second over the 45-minute clock | 0.5% |
| Unsafe boundary breach | Mission terminated |

In 2026 the official results report **55 flights and 21 crashes**. Roughly two flights in five ended in a crash, each costing half the demonstration score. The highest-expected-value aircraft is the one that finishes — which makes reliability, a conservative mission order, and a spare airframe worth more than any clever subsystem.

### Safety inspection checklist, treated as a design specification

TRUST certificate · FAA registration number on an exterior surface · Remote ID module · ≤35 lb · **fasteners secured with safety wire, Loctite or nylon nuts** · brightly coloured batteries · RTL and flight termination from both pilot and GCS · **automatic RTL after 15 s of lost link** · **automatic flight termination after 90 s** · payload ≤2 lb with no means of sustained flight · GCS showing map, waypoint threshold under 50 ft, knots, feet · no internet or cloud dependency for safety functions · nobody in the propeller arc, software disarm not accepted.

Aircraft must be ready **one hour before** the flight slot. Fail or miss the inspection and the team goes to the back of the queue.

---

## 6. Schedule

Four semesters. The deadline that actually matters is the **flight readiness video**, due around July of the competition year, and we want it submitted early for a better flight slot.

### Phase 0 — Oct–Dec 2026: restart and foundations

| Milestone | Owner |
| --- | --- |
| Members recruited and placed on sub-teams | Officers |
| **Two people pass TRUST; at least one starts Part 107** | Flight Test |
| 2026 handbook read end to end; compliance matrix drafted | Systems |
| Sponsorship packet out; HornRaiser live; department ask made | Business |
| **Wave 1 priced, not ordered**: vendor list, stock checks, live quotes, lead times | Business |
| Trainer airframe built, maiden flight in manual modes | Flight Test + Mechanical |
| ArduPilot QuadPlane parameters studied; SITL running missions | Software |
| Pack module schematic started (4 × ~97 Wh) | Electrical |
| **Gate: program kickoff review** — requirements baselined, budget approved | Systems |

### Phase 1 — Jan–May 2027: learn to fly it and prove the subsystems

| Milestone | Owner |
| --- | --- |
| **Trainer transitioning reliably**, hover and wing-borne, tuned | Flight Test + Software |
| Autonomous waypoint missions on the trainer, 1+ mile at weight | Software |
| **Conceptual Design Review** (Feb) — configuration frozen | Systems |
| First pack module built and load-tested; power board v1 fabricated | Electrical |
| Detection pipeline trained on our own mock tent and mannequin, running on the Orin | Software |
| Drop mechanism v1, drogue drop tests from the trainer in hover | Mechanical |
| **Preliminary Design Review** (Apr) — wing and structures design | Systems |
| Decision point: register for 2027 as a learning entry, or hold for 2028 | Officers |

### Phase 2 — Jun–Aug 2027: integration summer

| Milestone | Owner |
| --- | --- |
| Full mission flown in SITL, start to finish, two operators | Software |
| Live mosaic pipeline producing a judge-quality map from trainer flights | Software |
| Wind-corrected release solver calibrated against measured drops | Software + Mechanical |
| Wing structural test article built and load-tested | Mechanical + Manufacturing |
| ODM fallback timed on our own laptop with a realistic image count | Software |
| **Gate: subsystem readiness review** (Aug) | Systems |

### Phase 3 — Sep–Dec 2027: build the competition aircraft

| Milestone | Owner |
| --- | --- |
| **1–4 Oct 2027: attend SUAS 2027 at Skyway Range as observers.** Walk the course, watch inspections and missions, meet teams, photograph everything | Everyone who can go |
| **Critical Design Review** (Oct) against the published 2028 rules | Systems |
| Airframe #1 fabricated; toolless joints and transport case proven | Mechanical + Manufacturing |
| Avionics integrated; inspection checklist walked item by item on the bench | Electrical + Software |
| **First hover** (Nov), **first transition** (Dec) | Flight Test |
| Website rebuilt against the 40/40/20 rubric; report outline written | Systems + Business |

### Phase 4 — Jan–May 2028: prove it

| Milestone | Owner |
| --- | --- |
| **Register and pay the $1,250 by ~1 April** | Business |
| Transition envelope expanded; lost-link behaviour tested at 15 s and 90 s | Flight Test |
| Autonomous laps at full loaded weight; lap-count trials against the clock | Software + Flight Test |
| Delivery accuracy campaign to inside 50 ft, in wind, in heat | Mechanical + Software |
| Mapping validated end to end including the USB handover | Software |
| Airframe #2 (spare) built | Manufacturing |
| **Gate: dress rehearsal** (May) — full 45-minute mission, two operators, timed, nobody else touching the aircraft | Systems |

### Phase 5 — Jun–Sep 2028: submit and compete

| Milestone | Owner |
| --- | --- |
| **Flight readiness video shot and submitted early** (June, for flight-order advantage) | Flight Test |
| Pilot competence video submitted | Flight Test |
| **Technical design report and website submitted** with the fee (~July) | Systems |
| Heat testing: servos, adhesives, batteries at Texas summer temperatures | Mechanical + Electrical |
| Repair kit, spares, consumables, transport cases packed and inventoried | Manufacturing |
| Mission rehearsals until setup plus mission fits inside 45 minutes with margin | Everyone |
| **Compete** | Everyone |

---

## 7. Sub-teams

### Mechanical Design

Owns the airframe, the mechanisms, and the mass budget.

Projects: 4-piece wing with toolless joiners (L) · fuselage and payload bay (M) · booms, motor mounts, folding-prop integration (M) · **payload release mechanism and drogue (M)** · transport case and 3-minute assembly (M) · CG and mass properties tracking (S) · heat-soak testing of servos and adhesives (S)

Hard requirements: ≤15 lb all-up, toolless in 3 minutes with up to 4 people, fasteners secured with safety wire or Loctite or nylon nuts, nothing may fall off in flight, batteries removable without deconstructing the airframe. Packing tier: the 4-piece wing reaches **Carry-On, worth 25**; the 50-point **Personal Item** tier needs every piece inside 18 × 14 × 8 in. That 25-point gap is a live trade.

### Electrical and Power

Owns every watt on the aircraft.

Projects: **four ~97 Wh Li-ion pack modules (L)** · pack switch and power distribution board (M) · avionics power module (S, the onboarding Level 2 board) · harness with blind-mate boom connectors (M) · payload release driver board (S) · battery logistics, MSDS files, LiPo bags, charging procedure (S)

Hard requirements: every pack under 100 Wh individually, brightly coloured, removable without deconstructing the aircraft, no swaps during the mission clock.

### Software and Avionics

Owns the 1000 points that come from the mission.

Projects: ArduPilot QuadPlane configuration and transition tuning (L) · **one-button two-operator mission automation (L)** · tent and mannequin detection, trained on our own mock targets (L) · **live GPS-placed mapping pipeline (M)** · **wind-corrected ballistic release solver (M)** · detection-to-GPS georeferencing (M) · ground station meeting the inspection display requirements (M) · lost-link and failsafe configuration and testing (S)

Hard requirements: autonomous RTL at 15 s, flight termination at 90 s, no cloud dependency, deliveries only after a complete autonomous lap.

### Manufacturing and Operations

Owns making the parts and the field kit.

Projects: wing layup and tooling (M) · 3D printing and CNC of mounts, joiners, release housing (S) · spare airframe (M) · transport cases and foam cutting (S) · field repair kit and consumables inventory (S)

### Flight Test and Range Operations

Owns certifications, test discipline, and not crashing.

Projects: **TRUST and Part 107 certification (S)** · test site and university approvals (S) · test cards and flight log system (M) · transition envelope expansion campaign (L) · drop test campaign (M) · **lost-link and failsafe verification (S)** · mission rehearsals and two-operator drills (M) · qualification video production (S)

### Systems Engineering and Integration

Owns the compliance matrix, the budgets, and the documents that are worth 250 points.

Projects: **compliance matrix against the handbook, line by line (M)** · mass and time budget control (S) · **technical design report, 10 pages IEEE (L)** · **team website against the 40/40/20 rubric (L)** · risk register and safety case (M) · design review packages (M)

### Business, Outreach and Sponsorship

Owns the money and the logistics, both of which can end the season.

Projects: sponsorship packet and corporate outreach (L) · HornRaiser campaign (M) · department and student-government funding applications (M) · **registration and fee payment on schedule (S)** · travel, lodging and vehicle logistics for Tulsa (S) · **insurance and UT Risk Management clearance for UAS operations (S)** · procurement, quotes and inventory (M)

---

## 8. Procurement

**Nothing is ordered until the gate it hangs off has passed.** Each wave below is a priced shopping list, not a purchase order. Prices are estimates from September and October 2026 and must be re-quoted before any order; stock moves, and at least two items (the Pixhawk 6X and the Orin Nano Super) have already been out of stock once.

| Wave | Released by | Depends on |
| --- | --- | --- |
| 1 | Program kickoff review | Requirements baselined, budget approved, funding identified |
| 2 | Conceptual Design Review | Configuration frozen, trainer flying |
| 3 | Critical Design Review | 2028 rules published, design frozen, money in hand |
| 4 | Registration decision | Entry year confirmed, travel plan approved |


### Wave 1 — now through December 2026 (~$3,500–4,500)

| Item | Spec | Est. |
| --- | --- | --- |
| Trainer VTOL airframe | Small foam 4+1 quadplane, factory VTOL | $500–800 |
| Flight controller | Pixhawk 6X, or 6C at $165.99 | $170–450 |
| GNSS | L1/L5 receiver (M10 or F9P class) | $150–300 |
| Telemetry | RFD900-class pair | $300 |
| RC link | ELRS or Crossfire plus transmitter | $300 |
| Bench kit | Current-limited PSU, watt meter, LiPo bags, extinguisher, PPE | $500 |
| Trainer batteries and spares | Packs, props, servos, connectors | $500 |

### Wave 2 — January through June 2027 (~$2,500–3,500)

| Item | Spec | Est. |
| --- | --- | --- |
| Companion computer | Jetson Orin Nano Super 8 GB — **order early, it has been backordered** | $400 |
| Camera | Gimbal camera, downward, hardware trigger | $400–700 |
| Cells for pack prototypes | Molicel P45B 21700 × 12 | $200 |
| Pack hardware | Holders, BMS, bright cases, connectors | $250 |
| PCB fabrication | Pack switch, avionics power, release driver — 2 revisions | $600 |
| Drop test hardware | GP908-class beacon, bottles, drogues, servos | $300 |

### Wave 3 — July 2027 through March 2028 (~$7,500–9,500)

Competition airframe: lift motors and ESCs, cruise motor, folding props, wing materials and tooling, carbon booms, printing and CNC, second avionics set, 24 × P45B cells and pack hardware, mapping camera, spare airframe, transport cases.

### Wave 4 — April through September 2028 (~$4,000–5,500)

**Registration $1,250 · competition fee $250** · travel, lodging and vehicle for Tulsa · consumables, spares, repair kit.

**Program total: roughly $18,000–23,000 over two years.** Comparable published team budgets run $10k–30k per year, and Cornell's SUAS team raised about $32k for one season.

---

## 9. Risk register

| # | Risk | Mitigation |
| ---: | --- | --- |
| 1 | **We crash on mission day** (21 of 55 flights in 2026) | Conservative mission order, bank points early, spare airframe, envelope expanded on the trainer not the competition aircraft |
| 2 | **We miss the qualification video** — roughly a quarter of registered teams never fly | Target the video in June 2028, a month early, on hardware that has already flown the profile |
| 3 | Money does not arrive | Business team starts in October 2026, not in 2028; fees are $1,500 before any hardware |
| 4 | Transition crashes during development | Learn on a cheap foam trainer first; never develop transitions on the competition airframe |
| 5 | Heat and field conditions | Test in Texas summer; Calgary lost a mission to **servo hot glue melting at 39 °C** |
| 6 | Radio interference | A past UT Austin team saw range collapse from half a mile to a few hundred feet; test all systems powered together, at range, early |
| 7 | Mass budget creep past 15 lb | Mass under configuration control from Phase 0; every change priced in points |
| 8 | Mapping does not finish inside the clock | Live onboard mosaic as the guaranteed deliverable; ODM only as an offline upgrade, timed in advance |
| 9 | Rules change for 2028 | Compliance matrix re-run when the handbook publishes; nothing in the design depends on a single point value |
| 10 | Team turnover between the two years | Documentation and git discipline from the start; the onboarding ladder feeds the Electrical team |

---

## 10. To verify when the 2028 handbook publishes

| Item | Current understanding |
| --- | --- |
| Mission clock | 45 minutes including setup (section 3.1). Note the risk-mapping page says 30 minutes — an internal inconsistency in the 2026 handbook |
| Interoperability system | **Appears retired.** Not mentioned in the 2026 rules; the 1 Hz telemetry requirement came from 2021 AUVSI-era rules |
| Scoring formulas | Operators, rapid response, endurance and delivery formulas as quoted here, all from the 2026 handbook |
| Fees | $1,250 + $250 in 2026 |
| Event dates and venue | 1–4 Oct 2027 at Skyway Range; 2028 unannounced |
| Delivery altitude floor | 150 ft AGL minimum, confirmed as a flight rule |
