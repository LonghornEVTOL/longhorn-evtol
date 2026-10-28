# The 2026 handbook, read in full

**Status:** v1, complete. Every other document in this folder cited the 2026 rules second-hand. This one is read directly from the published handbook, all 35 pages of it, and it is now the authority. Where another document in this folder disagrees with this one, this one is right.

**Source.** RoboNation publishes the SUAS handbook as a GitBook, not a PDF: `robonation.gitbook.io/suas-resources`. Every page is also served as markdown by appending `.md`. Retrieved 2026-10-27. The handbook's own change log records seven revisions, the last on **2026-09-14, the week of the event**, which replaced the search boundaries.

**Theme for 2026: Storm Response.** Disaster recovery and relief. The 2027 theme is already announced as Island Harbor and Coastal Environment.

**What to re-check in 2028.** Everything here is 2026. [Rules watch](rules-watch.md) says which requirements have survived every rewrite and which churn.

---

## 1. The scoring, confirmed

1,250 points: 250 of Design Documentation and 1,000 of Mission Demonstration.

| Deliverable or task | Points |
| --- | ---: |
| Technical Design Report | 150 |
| Team Website | 100 |
| Flight Endurance | 250 |
| Efficient Operators | 200 |
| Design for Rapid Response | 200 |
| Search, Detect and Deliver | 200 |
| Risk Mapping | 150 |

Formulas, verbatim:

- **Flight Endurance.** 20 points for at least one successful takeoff *and* landing, manual or autonomous. 30 more if all takeoffs and landings are autonomous. Plus `200 × (N_L / 10)²` where `N_L` is complete laps.
- **Efficient Operators.** `200 × min(1, (4 − O) / 2)`, O = operator count.
- **Design for Rapid Response.** All-up weight `50 × min(1, (35 − W) / 20)` with W in lb · volume tier 50 / 25 / 10 · toolless assembly 25 · every battery under 100 Wh 75.
- **Search, Detect and Deliver.** Per object, 100 each: survives within the vicinity of the Search Boundary 20, lands within 50 ft of a target 50, correct object to the correct target 30.
- **Risk Mapping.** A sliding 0 to 150 on quality. No formula.

---

## 2. The flight area, with the actual coordinates

### Flight Boundary

An 11-point polygon. The UAS must stay inside it and inside **150 ft to 400 ft AGL**. Outside either and the mission is terminated.

```
36.2193143935478, -96.00171089172362
36.2203876816226, -96.0041356086731
36.2208031440207, -96.006817817688
36.2205954130975, -96.0094571113586
36.2198164172223, -96.01181745529171
36.2181891563676, -96.0142421722412
36.2149951257415, -96.0109269618988
36.2128397305797, -96.0107660293579
36.2128051052733, -96.00916743278502
36.2106063669373, -96.00924253463749
36.2106583063506, -96.00173234939579
```

That spans about 1,130 m north to south and about 1,120 m east to west, which confirms the figure the [FAA compliance](faa-compliance.md) document uses to argue the whole competition is a visual line of sight problem.

Below 150 ft AGL is permitted **only for takeoff and landing**, and never while over another runway occupied by another team. Separately, §3.0.1 requires staying above 150 ft AGL whenever more than 500 ft from the runway.

### Search Boundaries, and the answer to a question we had open

**There is one Search Boundary per runway, about 10 acres each, and it is used for BOTH Risk Mapping and Search, Detect and Deliver.** Risk Mapping's area of interest *is* the Search Boundary. The targets are in it, so the drop zone is the same ground as the mapping area. This was an open question in earlier drafts and it is now closed.

Search Boundary 1, replaced on 2026-09-14:

```
36.211822, -96.005545
36.211822, -96.002627
36.213147, -96.002627
36.213147, -96.005545
```

Search Boundary 2:

```
36.213469, -96.002635
36.215795, -96.002635
36.215795, -96.004286
36.213469, -96.004286
```

Both are axis-aligned rectangles of roughly **150 m by 260 m**, which is about 9.5 acres. That is a small area: at 23 m/s a single pass across the long axis takes 11 seconds, and the whole box fits inside about 40,000 m². Our 4-minute mapping budget is generous rather than tight, and the detection problem is a 10-acre search, not a square-kilometre one.

### Runways

Two independent runways run missions simultaneously, and a team flies only its own.

| | Surface | Supports |
| --- | --- | --- |
| **Runway 1** | 40 ft by 500 ft, paved | VTOL and HTOL |
| **Runway 2** | 40 ft by 40 ft, paved | **VTOL only** |

**Runway 2 is a 40 ft square pad that a fixed-wing team cannot use.** That is independent confirmation of the quadplane decision in [suas-2028](suas-2028.md): half the available flight slots are VTOL-only.

Note a contradiction inside the handbook: §3.0.1 states the aircraft must be able to take off and land in a runway "approximately 70ft by 500ft" while §3.0.2 gives Runway 1 as 40 ft by 500 ft. Design to the smaller number.

### Airfield data, all new to our research

- Elevation inside the Flight Boundary varies **633 ft to 748 ft MSL**, runway about **690 ft MSL**. A 115 ft terrain range under a 150 ft AGL floor means AGL and MSL diverge materially across the course.
- Average magnetic deviation **2 degrees west**.
- **Trees up to 100 ft AGL** inside the boundary. The handbook explicitly says to consider this when setting waypoints over trees.

---

## 3. Order of tasking, which is stricter than we assumed

This is the single most important correction in this document.

> Teams must fly **all** waypoint laps they wish to attempt **prior to entering the Search Boundary**. In addition, at least one waypoint lap must be flown prior to entering the Search Boundary. Once the team has completed all the laps they wish to fly, they may decide the order of all other tasks.

And the enforcement, from §3.2.2:

> Once the UAS enters the Search Boundary with a purpose of performing either task, **the number of laps flown will be locked** and no additional laps will count towards the mission score.

**So laps and the search tasks cannot be interleaved.** You fly every lap you intend to fly, then cross into the Search Boundary once, and from that moment the lap count is frozen. After that, mapping and delivery may be done in either order. The handbook's own example flow is takeoff, four laps, Risk Mapping, Search Detect and Deliver, land, clear the runway.

Two consequences:

**The lap count is a single irreversible decision made in the air.** There is no banking laps, trying a delivery, then returning for more. The commit point is the Search Boundary edge, and crossing it early to get the mapping stitch processing costs every remaining lap. Our mission timeline has to put all laps first.

**Partial laps are worthless and a manual takeover restarts the lap.** Any transition to manual, other than takeoff or landing, requires returning to the start of the lap. If the aircraft lands and takes off again it must fly at least one full lap before attempting anything else.

---

## 4. Mission timeline

**Mission Time is 45 minutes** and setup is inside it. Several internal links in the handbook point at an anchor slug reading "mission-time-30-minutes", which is a stale slug from an earlier draft; the section heading and body both say 45.

Sequence of events:

1. **At Check-In, before Mission Time**, teams are given the waypoint sequences. Teams receive the sequences for **both** active flight lines and must be ready to fly whichever they are assigned.
2. At the start of Mission Time the Lead Judge asks the team to identify its operators, then hands over **the two delivery objects and a USB drive**. The USB drive is the judges', not ours.
3. Judges start the clock when the airspace and runway are available, **regardless of team readiness**. No powering on the aircraft or GCS before it starts.
4. The clock stops when the team has cleared the runway, relinquished the airspace, and submitted whatever deliverables it is attempting. Ending early earns nothing. Going over costs 0.5% of demonstration points per second.
5. **Tear Down Time is a separate 10 minutes.**

**Air traffic or weather pause.** The mission pauses where it stands, the aircraft lands, and the team gets a penalty-free timeout of at least 10 minutes. During it the team may refuel but may **not** otherwise modify the UAS, load additional payloads, or process data. Battery swaps, otherwise banned on the clock, are allowed here. On resumption the aircraft takes off, returns to the point of interruption, and continues from there.

---

## 5. Each task, as written

### 5.1 Flight Endurance

Up to 15 waypoints form one lap of approximately 2 miles. Teams choose to fly 1 to 10 laps. Waypoints must be hit within 100 ft, in order, fully autonomously, with no intermediate manual takeover or landing.

"Autonomous" takeoff and landing has a precise definition: **only a single command may be issued** by the Safety Pilot or GCS Operator to commence the action. Any intervention beyond one button press is at the judge's discretion and may void the autonomy bonus. A takeoff counts once the aircraft is above 150 ft AGL; a landing counts if it touches down without damage to the aircraft or the surroundings.

### 5.2 Efficient Operators

Two to four operators. Minimum two: a Safety Pilot and a GCS Operator, dedicated respectively to manual override and autopilot operation. **Neither may perform any other task while the UAS is in flight, and doing so terminates the mission.**

> Team members who don't have an operator role can only assist with teardown. While on Mission Time, any non-operators must stand to the side, not communicate or assist the operators, and observe only.

**This is an operational constraint we had not captured: on a two-operator crew, two people do the entire 45 minutes including assembly.** Non-operators cannot help set up. Operator and observer assignments are fixed before the mission starts, cannot change, and must be declared to the judges.

Up to 8 team members may be on the flight line, of whom up to 4 may be operators.

### 5.3 Design for Rapid Response

Scored **at the Safety Inspection**, not during the mission.

All-up weight is the maximum takeoff state including all batteries, payloads and delivery objects. 35 lb ceiling, full marks at 15 lb or less.

Volume tiers, which we had mis-stated:

| Tier | Dimensions | Points |
| --- | --- | ---: |
| **Personal Item** | 18 × 14 × 8 in | **50** |
| Carry-On | 22 × 14 × 9 in | 25 |
| Check In | 27 × 21 × 14 in | 10 |

**Our plan targets the Carry-On tier, which is 25 points, leaving 25 on the table.** And the Personal Item tier is harder than it looks for our configuration: a 1.9 to 2.1 m wing in four sections gives panels of roughly 19 to 21 in, which does not fit the 18 in longest dimension. Reaching 50 points needs five or more wing sections, or a shorter span. That is a real trade for the Preliminary Design Review.

The unpack demonstration is **3 minutes, up to 4 personnel**, from fully collapsed to in-flight state with motors and control surfaces operating. Toolless, hands only, earns the extra 25. The handbook then says that a team which cannot finish inside the time or personnel limit "will not achieve any points related to this task", which is ambiguous about whether that zeroes the volume tier alone or all 200. Treat it as a hard gate and ask the organisers.

Batteries: "There is no limit to the number of batteries that a UAS can be outfitted with. **Each individually packaged battery onboard the UAS must be below the 100 Wh limit** for this task." The phrase *individually packaged* is what makes the club's four-module architecture work, and it largely answers whether series-connected modules read as one battery: the unit of assessment is the package, not the electrical path. Confirm the arithmetic convention for Wh in writing anyway.

### 5.4 Risk Mapping

A stitched image of the Search Boundary, submitted on the judges' USB inside Mission Time, `.png` or `.jpeg`, filename including the team name. Late is zero.

Scored on coverage, projection accuracy, stitching "and other quality signals". The published anchors: a high-quality map is "indiscernible from a professional-quality map seen on services like Google Maps" and scores about 150; a medium-quality map has minor stitch errors, varying exposures and minor missing coverage but still works as a map, and scores about 75; insufficient quality scores zero. The handbook shows worked examples from Nirma University Team Arrow at the high end and Hacettepe Markut at the medium end.

### 5.5 Search, Detect and Deliver

**Objects.** A GP908 strobing beacon with three AAA cells installed, about 155 g, labelled with the team's identifier, and RoboNation publishes an STL and photographs of it. And an 8 oz plastic water bottle, about 255 g, with no specified brand, 2 to 2.5 in diameter and 5 to 6 in tall. The handbook explicitly says to design for the variation between vendors' bottles.

**Pairing.** Water bottle to the mannequin, beacon to the tent.

**Targets.** One mannequin and one tent, scattered among other debris inside the Search Boundary. The tent is open and of the pop-up variant. The mannequin may be in any orientation, laying down, sitting up, face-down, and will be surrounded or covered by bushes, trees, vehicles and similar.

**Judges are in the Search Boundary** to score drops, the ground may be marked to show the 50 ft radius, and targets may be **temporarily occluded while judges clear debris and score another team's drops**. So occlusion is operational as well as natural.

**Constraints.** Carry all objects simultaneously; landing to reload is prohibited. Release above the 150 ft AGL floor. At least one full lap first, or the delivery scores zero. Each delivery payload is 2 lb or less, where "the delivery payload includes everything that separates from the UAS during a delivery, including the object being dropped." No means of sustained flight. Must land undamaged and be safe for humans to be present in the drop area, and safe to retrieve and handle. **Freefall with no retardant mechanism is not successful.**

Three rules we did not have:

- **An attempted delivery is any payload released in flight**, successful or not.
- **"If the UAS were to drop multiple payloads at once, only the best scoring dropped payload will be used for scoring."** No shotgunning both objects at one target.
- **"Judges must be able to safely and easily retrieve and separate the delivery object from the delivery payload to verify that it's undamaged. Separation must not require tools or any instructions. If the judge is unable to separate the delivery object, then the drop will not count."** The release mechanism design has a usability requirement: a judge who has never seen it must free the bottle from our canopy by hand, with no explanation.

---

## 6. Penalties, complete

Penalties are percentages of achievable component points and are **unbounded**. If penalties exceed points the demonstration is zero, and "teams cannot score points while generating a penalty."

| Event | Penalty |
| --- | --- |
| Excess time | 0.5% per second |
| Things fall off the aircraft | 10% per item |
| Crash or collision | 50% per event |
| Unsafe operations, including failing to obey a judge's manual-takeover or kill command | 50% per infraction |
| Unsafe out of bounds | Mission termination and return to launch |
| **Manual takeover** | **Restart the lap** |

Two details worth knowing:

- **Collision fault matters.** "If a team is in their runway's dedicated airspace, then they will not receive a penalty for the collision, and only the offending team will receive a penalty."
- Foreign object debris such as nuts and bolts must be cleared from the operating area before mission flight time stops.

---

## 7. Design Documentation, with the open question closed

### Technical Design Report, 150 points

Sections: Abstract, Acknowledgements, Competition Strategy, Design Strategy, Testing Strategy, References, and an optional Test Plan and Results appendix.

**Format: 10 page limit including References and Appendices.** IEEE Conference Proceedings format, PDF.

**That closes the biggest open question in [documentation strategy](documentation-strategy.md), and the answer is the unfavourable one.** The optional appendix does *not* sit outside the page limit in SUAS, so it is not free structural space. It is still worth writing, because "excellence seen in this section can be eligible for a special judges' award", but it costs pages against the five scored sections.

Weights, which confirm the rubric we had reconstructed from RobotX exactly:

| Section | Weight |
| --- | ---: |
| Formatting | 5% |
| Abstract | 10% |
| Competition Strategy | 25% |
| Design Strategy | 25% |
| Testing Strategy | 25% |
| Acknowledgements | 5% |
| References | 5% |

Formatting has only Strong or Requirements Not Met. No partial credit.

The band wording we were writing against is confirmed verbatim. Testing Strategy's Outstanding band is distinguished from Strong by one phrase, "**in accordance with a test plan**", plus component and sensor testing done independently. Competition Strategy's Outstanding band requires "detailed discussion on trade-off studies between system complexity and reliability", and the prompt frames it as the choice between adding capabilities and improving the reliability of existing ones.

Two instructions we did not have:

- Design Strategy's Outstanding band turns on the word **creative**: "clearly identifies creative aspects of system. Creative design methodology is justified with required calculation steps and visual aids. Content clearly exhibits a Systems Engineering approach."
- And a page-saving rule: Design Strategy "should not include detailed component descriptions and/or specifications not of original design." Do not spend pages on COTS datasheets.

### Team Website, 100 points

Team Information 20%, **Vehicle Design Documentation 40%**, Website Quality 40%. Note the criterion keeps the older name in SUAS 2026, unlike RobotX which renamed it to Vehicle Design and Testing, but its Outstanding band already reads "vehicle development **and testing** process is thoroughly documented".

Required content: current team name and contact information, vehicle photos or video, supporting media, and a list of sponsors with logos.

Required quality attributes, listed in the handbook itself: English, clear prioritisation of key content, **site search functionality**, contrast and repetition and alignment and grouping, **user accessibility as defined by the W3C Web Accessibility Initiative**, cross-browser compatibility, and a mobile-friendly display.

Search and accessibility are named requirements, not inferences. The survey in [documentation strategy](documentation-strategy.md) found working search on six of eighteen competitor sites and accessibility broadly broken, so both remain open goals.

SUAS 2026 does **not** carry the mandatory "test event summaries showing time in the field" website item that RoboSub and RobotX added for 2026. That strengthens it as a prediction for the SUAS 2027 handbook rather than a current requirement.

### The two videos

**Proof of Flight Readiness**, 5 minutes maximum. English or English subtitles. School and team name at the start. Roughly 30 seconds of introduction with visuals of the whole development team, 90 seconds on safety risks and mitigations covering both development and the mission, and 180 seconds of autonomous proof of flight: takeoff above 50 ft AGL, transition to autonomous, **at least 1 mile at full loaded weight in a single flight**, waypoints within 100 ft, turn radius 150 ft or less, climb and descent angles to 20 degrees, then landing.

**Proof of Pilot Competence**, 3 minutes maximum, and new detail for us: manual takeoff above 50 ft AGL, **manual flight to and from a point more than 1,000 ft from the Safety Pilot**, and a manual landing. Teams with more than one candidate Safety Pilot may submit several to get multiple pilots approved.

Both are reviewed on a rolling basis. Videos are hosted by the team on YouTube, Vimeo or the team website.

---

## 8. Two things that change the strategy

### Every team gets up to two flights, and the better one counts

From What's New for 2026: "we will do all we can to provide every team two flights." From §3.0.7: "As time allows, teams will be given the opportunity to fly again for a second chance in the same flight order. **The better of the two demonstrations will count.**"

That is why the published scoresheet has 61 team-rounds for 27 scoring teams, and why [rules watch](rules-watch.md) reads it as best-round-per-team.

It also changes the risk calculus. A crash on the first flight costs that round, not the season, provided the aircraft survives well enough to fly again, which is exactly why the program plan sends a spare airframe. The sensible shape is a conservative first round that banks points and a more aggressive second round, and the handbook permits a backup instance of the same design.

### Flight order decides who is invited at all

Flight order follows the order in which Proof of Flight Readiness videos are **successfully qualified**, and "**up to 50 teams will be invited to the on-site competition based on flight order.**" Later submissions are waitlisted and admitted at the organisers' discretion. 85 teams registered for 2026 and 64 qualified.

So submitting the readiness video early is not merely a weather optimisation, it is how a team secures a place. Our program plan already front-loads it; this is the reason to treat that deadline as the hardest date in the season.

---

## 9. Vehicle and safety requirements

### Hard gates

Battery electric only, no fuels, no exotic batteries. Heavier than air, free flying, untethered. 35 lb or less all-up. Registered on FAADroneZone with the certificate presented at both safety inspection and the flight line, and the registration number on an external surface. Remote ID broadcasting a unique vehicle ID and position. Safety Pilot holds TRUST and presents it at both points. Manual takeover available at any time.

**The Safety Pilot may be a student, the adviser, or a non-student.** That is explicit, and it means a faculty adviser can hold the stick.

### Design lock, corrected

One design, locked at Proof of Flight Readiness submission. Acceptable later changes are appearance, replacement of damaged components, and adding a Remote ID module. Unacceptable changes are anything touching core capabilities such as propulsion or avionics, or **anything changing size or weight by more than 5%**.

> The team may use backup instances of that design during development **and bring backup systems to competition** — but only one can be used during a single Mission Demonstration attempt.

**Earlier drafts of [suas-2028](suas-2028.md) said backup airframes were for development only. That is wrong.** A spare airframe may travel to Tulsa and fly, which with the two-flight rule makes the spare a genuine strategy rather than insurance.

### Failsafes, with the termination spec

Autonomous RTH or RTL, and autonomous flight termination. **Both** the Safety Pilot and the GCS Operator must be able to trigger each. Automatic RTH or RTL after **15 seconds** of comms loss; automatic flight termination after **90 seconds**.

The termination state is specified: "Fixed wing: throttle closed, full up elevator, full right rudder, full aileron, and full flaps down. Rotary: throttle closed."

### Ground station and ground equipment

The GCS display must be continuously visible to the GCS judge and must show the flight boundaries, the UAS position, all other competition elements, ground speed **in knots**, altitude **in feet AGL**, and a **waypoint threshold configured to less than 50 feet**. If the judges cannot see the display the aircraft is not permitted to take off or is recalled.

Nothing taller than 15 ft. **No ground-based imaging sensors may substitute for the aircraft's imaging payload.** Safety-critical functions must run on onsite systems with no dependency on anything outside the team's control, explicitly including the public internet and public cloud, and the list of safety-critical functions names return to land, flight termination, manual piloting, commanding the autopilot, **and the failsafe for deliveries**.

### Batteries

Brightly coloured, bright tape preferred. Removable without deconstructing the airframe, so packs cannot be embedded in structure. No landing to swap or recharge on the clock, except during a weather or traffic pause.

And a documentation requirement with a sting for a club-built pack: teams must submit "battery specifications, Material Safety Data Sheets (MSDS), and proper disposal procedures, **sourced from the battery manufacturer** for all batteries", keep hard copies on site, bring adequate LiPo safe bags, inspect daily for swelling, heat, leaking, venting or burning, and have a member present whenever batteries are charging.

**A club-designed pack has no manufacturer MSDS.** The workable reading is the cell manufacturer's MSDS plus our own specification and disposal procedure, but this is the one compliance question that could cost the 75-point battery play at the inspection table, and it should be asked in writing well before the event.

### Obstacles and RF

Obstacle avoidance is not a scored task. Up to two aircraft fly at once, teams communicate through their judges, and "the operations at the competition will not actively put teams in a situation that causes UAS to be on a known collision path."

RF must be FCC compliant, any FCC-permitted band may be used, and **the judges provide no spectrum management**. Judges use 462 MHz handhelds. The handbook recommends hardwired connections where possible, encryption, directional antennas, RF filters, frequency hopping or dynamic channel selection, and warns that other teams will be running the same autopilot, so guard against invalid connections. Intentional jamming is cheating.

### Environment

Average September wind in Tulsa about 14 mph. Temperatures to 110 °F. No flying in precipitation but equipment must be secured quickly against sudden rain. Fog acceptable above 3 miles visibility. Lightning sends everyone to the parking lot.

---

## 10. Logistics, eligibility and the things that are easy to get wrong

**Eligibility.** 75% or more full-time students. Majority college or high school; middle school members permitted; up to 25% alumni, industry, academic or government partners. Up to 30 people may be registered. Minimum two for safe operations.

**Rule 6, which is easy to miss.** One student is designated team lead, must be conversationally fluent in English, and "**the team lead, and only the team lead, will speak for the team during the mission demonstration.**" Read together with the operator rules, the team lead has to be one of the two operators, since a non-operator may not communicate during Mission Time.

**Rule 7.** At least one representative at team orientation, or the team may not deploy.

**Safety inspection.** Be ready at least one hour before the estimated flight time. Fail or be absent and the team goes to the back of the queue.

**Fees.** $1,250 registration due 1 April, $250 competition fee due 29 July if approved to fly. Both non-refundable. Teams not approved to fly may still attend and owe no competition fee.

**Dates for 2026.** Registration 1 Dec 2025 to 1 Apr 2026. Proof of Flight Readiness 1 Apr to 22 Jul, extended from an earlier date. Proof of Pilot Competence 1 Apr to 1 Sep. The 29 Jul deadline carries the competition fee, team member registration, demographics, vehicle information, on-site requirements, the TDR and the website. Check-in, orientation and safety inspections on the afternoon of 14 Sep, mission demonstrations 15 to 17 Sep, awards the evening of 17 Sep.

**Vehicle information is locked at registration**, not at the readiness video: "the details specified in this form must not change after this point without written approval from the organizers."

**On site.** Team Village gives each team a 6 ft table under a tent with one 120 V 60 Hz 15 A outlet, and batteries may be charged during the day but not overnight. The flight line gives a 10 × 10 ft tent with one table and a 120 V 60 Hz 10 A outlet. Competition staff move the aircraft from the pits to the flight line on flatbed trailers, so the team must be able to carry everything between table, trailer and flight-line tent. The event is open to the public.

**Awards.** Prize money by Mission Demonstration ranking and by Design Documentation ranking, paid within four to six weeks. Four special awards are nominated on site by teams, judges and staff: the Dawn Jaeger Tenacity Award, the Dr. Arthur Reyes Safety Award, the JustJoe Sportsmanship Award, and the **Most Innovative Award**. Innovation is therefore a nomination-based trophy, not points, which is the context for any decision about building clever hardware that the scoresheet does not pay for.

---

## 11. Appendix D is a placeholder, and that is a signal

The 2026 handbook carries an **Appendix D: Moving Object Task** whose entire content is "TBD".

Nothing was scored against it in 2026. But an empty, titled appendix is the organisers telling us what they were considering and did not finish. A moving-object task is the first thing in the handbook's history that would demand tracking rather than searching, and it is the one plausible future requirement that would change the perception architecture from a detection problem into a latency problem.

Watch for it in the 2027 handbook. [Rules watch](rules-watch.md) carries the rest of the forward-looking analysis.

---

## 12. What this document corrected

For anyone who read the earlier research:

1. **All laps must be flown before entering the Search Boundary, and entering locks the lap count.** Laps and search tasks cannot be interleaved.
2. **The Search Boundary serves both Risk Mapping and Search, Detect and Deliver.** Same ground, about 10 acres, roughly 150 m by 260 m.
3. **The TDR's 10-page limit includes References and Appendices.** The Test Plan and Results appendix is not free space.
4. **Backup airframes may be brought to competition and flown**, one per attempt.
5. **Every team gets up to two mission demonstrations and the better one counts.**
6. **Up to 50 teams are invited on site, ordered by qualified readiness-video submission.**
7. **The 50-point volume tier is Personal Item at 18 × 14 × 8 in, not Carry-On.** Our four-piece wing reaches only the 25-point tier.
8. **The unpack demonstration is scored at the Safety Inspection with up to 4 people, but during Mission Time only the declared operators may touch the aircraft.** A two-operator crew assembles the aircraft with two people.
9. **The 2 lb delivery payload limit covers everything that separates**, and a judge must be able to free the object by hand with no tools and no instructions or the drop does not count.
10. **Only the best-scoring payload counts if several are released at once.**
11. **A manual takeover restarts the lap**, and a collision inside your own runway's airspace is not your penalty.
12. **New airfield data:** 633 to 748 ft MSL, 2 degrees west magnetic deviation, 100 ft trees, and a 40 × 40 ft VTOL-only second runway.
13. **Battery MSDS must come from the manufacturer**, which is an unresolved question for a club-built pack.
