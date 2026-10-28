# Documentation strategy

**Status:** Partial, v1. The survey of competitors' websites is complete and is the actionable part. The published TDR and website rubrics in full, the IEEE format mechanics and the transferable evidence from other competitions are outstanding — see section 6.

**Worth 250 of 1250 points** — Technical Design Report 150, website 100 — and winnable before the aircraft flies.

Website rubric weights: **40% development-and-test documentation, 40% site quality** (navigation, mobile, W3C accessibility, search), **20% team information.**

---

## 1. Mission rank and documentation rank are genuinely independent

- **Calgary placed 34th in mission and 2nd of 49 on report plus website** (2025).
- **IIT Madras won both documentation prizes in 2026 while placing mid-field.**
- **KFUPM won the mission outright with a website carrying `lang="zxx"`, fourteen `<h1>` elements and no search**, and a TDR that ranked 11th.
- 2025 mission champion Team Arrow has no `<h1>`, no `alt` attributes, no landmarks, no search and undated blog posts.

**This is a separable 250 points.** Nothing about winning the flying earns them, and nothing about losing the flying forfeits them.

---

## 2. The pattern that wins: dated, categorised, bylined design-decision records

Not a diary, not a brochure. The strongest sites publish **individual decision records with an author and a date, filterable by subsystem**, and the slugs carry the iteration history.

**Hacettepe PGM Simurgh-Y** (`simurgh-y.com`, 2nd in mission) is the best example found anywhere: **30+ dated, author-bylined design-decision records**, hierarchically categorised as e.g. `MECHANICS > DESIGN DECISIONS > PAYLOAD MECHANISM`, filterable by Software / Electronics / Mechanics and sortable by date. The URL slugs *are* the history — `payload-mechanism-1/-2/-3`, `propulsion-arm-1/-2/-3/-4`, `body-design-1/-2/-3/-4`, `motor-mount-1/-2`, `landing-gear-1/-2`.

Their titles show what a decision record reads like:

- "Payload Mechanism: Initial Concept Exploration — Our first payload drop concepts, magnetic braking, N10 winch motors, and servo hatches — **and why each was abandoned**."
- "Payload Mechanism: Dual-Motor Approach — Upgrading to 25 mm DC motors for higher torque — **and why the dual-motor gearbox system exceeded our MTOW budget**."
- "Motor Mount: Anti-Slip Solution — How we solved the torque-induced slip between PLA brackets and carbon fiber tubes."

Others doing the same: **Cornell CUAir** (35 posts across 7 sub-teams), **Calgary** (25+, dual-tagged), **KFUPM** (19+ across 6 categories).

### The one site written explicitly against the rubric

**Warsaw UT Absolute Edge** (`absoluteedge.pl/suas`, 3rd in mission) numbers its information architecture to match the deliverable: `01 Team · 02 Vehicle · 03 Design Process · 04 Procedures · 05 Build Log · 06 Videos · 07 Sponsors · 08 Contact`.

**Its build log carries hard numbers, which almost nobody else's does:**

- "Flight test campaign — 12 sorties in three days — Airdrop hit a **0.4 m CEP** across the campaign. One hub cracked on a hard landing and was reprinted overnight, which is exactly the failure path the frame was designed for."
- "Airdrop mechanism — **20/20 on the bench** — twenty consecutive releases with no misfires, including **three deliberate power-cut tests to confirm it fails closed**."
- "Onboard inference running at 22 FPS."

**Five decision records, DEC-01 to DEC-05, each with Options / Problem / Decision / Why / and an explicit `Evidence` field** — "flight testing measured a peak CPU temperature of 71 °C against an 80 °C limit"; "stable links at 2 km with 0% packet loss; loss-of-telemetry failover completed within 0.35 s in fault-injection testing"; "thrust-stand and hovering flight logs showed a 25% increase in overall flight time."

**And a Procedures section that reproduces the checklists with tolerances** — "arm-hub bolts to 2.5 Nm", "cell voltages within 0.02 V", "GPS lock ≥ 12 satellites, HDOP < 1.2", "geofence tested at 14 m/s entry speed, maximum boundary overshoot measured at 1.40 m."

**That is the model to copy.** Note how directly it maps onto our own [test and verification](test-and-verification.md) gates — the same artefacts, published.

---

## 3. Publishing failures correlates with the top of the field

The best sites write up what went wrong:

- **CUAir** publishes an "Incident Report: Hermes Tilt Rotor Horizontal Maiden Attempt" and the five-week rebuild that followed.
- **Absolute Edge** names the glue joint that destroyed their aircraft before its first launch — "the glue was at fault: one arm was bonded more weakly than the others" — the two-day rebuild from spares and 3D prints, and the five rival teams who donated parts.
- **Simurgh-Y** titles posts "and why each was abandoned."
- **AGH Solar Plane** records that "tests of the first prototype revealed that the drone was too heavy and energy-inefficient," and later that they "eliminated solutions that had failed in the previous prototype, avoiding repeated mistakes."

**IIT Madras, which won the website award, is the notable exception and publishes no failures at all.** So failure write-ups are not strictly necessary to win — but every strong site except the winner has them, and they are the cheapest way to fill a rubric category weighted at 40% for development and test documentation.

**AGH does one thing nobody else does: attaches per-milestone technical PDFs.** Four of them, on 3D-print materials, the power distribution system, an optimal drop-position algorithm and canopy design. The materials document carries real property data — PA12+CF15 at 7995 MPa tensile modulus and 73 kJ/m² Charpy impact, with a layer-adhesion analysis and a documented cap redesign.

---

## 4. Three open goals in the rubric

**Nobody uses tables, and almost nobody plots.** Across about eighteen sites surveyed, **only two `<table>` elements were found in total**, and **no site loads a charting library.** KFUPM's plots are rasterised JPEGs; one team's entire progress blog is `alt=""` JPEGs with zero machine-readable text. For a rubric category weighted 40% on development and test documentation, **presenting real test data in real tables and real charts is nearly uncontested.**

**Working site search exists on only six of eighteen sites.** Absolute Edge's is shipped broken — the overlay literally renders "Search index not found. Pagefind builds the index from prerendered HTML — run npm run build," meaning a dev build was deployed to production. KFUPM, Calgary, Team Arrow, AGH, KNR and Embry-Riddle have no search at all. **Search is explicitly named in the rubric.**

**Accessibility is broadly broken, and duplicate `<h1>` is the single most common defect:** PURA 16, KFUPM 14, Absolute Edge's parent club site 9, Bronco ASTRA 5, ATILIM 4. **Warsaw's KNR Drone homepage has no heading elements of any level, zero ARIA attributes, no `<main>`, and only four `href` attributes in the entire document** — the nav labels are not links, so the site is unnavigable by keyboard or crawler, and it declares `lang="en"` on wholly Polish pages, a direct WCAG 3.1.1 failure.

**Skip links appear on only five sites.** Correct single-`<h1>` hierarchies on only seven: IIT Madras, AmadorUAVs, Simurgh-Y, AGH's Świerszcz page, Calgary, CUAir and Absolute Edge's `/suas` page.

**And two of the 2026 top-five mission teams ship a broken front door.** AGH Solar Plane's root URL returns **HTTP 500** from Vercel — only deep links work, so a judge typing the domain gets an error page — and their own university directory still advertises a dead legacy domain.

---

## 5. What this means for us

We already have the right stack: a GitHub organisation with markdown docs in a repo, and a Next.js site on Vercel. The recommendation follows directly from section 2.

**Make the website a byproduct of engineering work, not a separate scramble.**

1. **Decision records as markdown in the repo, rendered by the Next.js site.** One file per decision, with front matter for date, author, subsystem and status, and an explicit **Evidence** field as Absolute Edge does. Slugs numbered per subsystem so the iteration history is legible from the URL list alone.
2. **Test reports written as the tests happen**, from the [test and verification](test-and-verification.md) gates. Every gate exit criterion is already numeric; publishing the measured value against it is the entire 40% category.
3. **Real tables and real charts.** Nearly uncontested, per section 4.
4. **Accessibility in CI.** Single `<h1>` per page, a skip link to `<main>`, `lang` correct, alt text on every image, and axe or Lighthouse as a build gate. Several competitors are losing points to defects a linter would catch.
5. **Working search, built in the production build.** Pagefind is a reasonable choice — just do not repeat Warsaw's mistake of deploying a dev build.
6. **Never let the root URL break.** Add an uptime check on the front page specifically.
7. **Publish the failures.** We will have them, and the field evidence says the strong teams write them up.

The TDR then draws on the same corpus rather than being written from scratch in July.

---

## 6. The rubric, recovered from RoboNation's other competitions

**Confirmed in full against the SUAS handbook, see [handbook 2026](handbook-2026.md). The reconstruction below was correct, and is kept because it explains how the rubric was recovered before the handbook was read directly.**

**RoboNation runs one house rubric across RoboSub, RoboBoat and RobotX — identical section list, identical weights, identical score-band wording. Only the page limit differs: RoboSub and RoboBoat 6 pages, RobotX 10.** SUAS is a RoboNation program, and the RoboSub handbook even hyperlinks the SUAS competition site.

**So the RobotX 10-page variant is the closest published analogue to the SUAS 150-point, 10-page TDR, and it almost certainly reflects the language SUAS judges are handed.** That recovers a rubric we had assumed unpublished.

### It also answers our open question about the missing 25%

The house rubric, weighted against a 200-point report:

| Criterion | Weight |
| --- | ---: |
| Formatting | 5% |
| Abstract | 10% |
| **Competition Strategy** | **25%** |
| **Design Strategy** | **25%** |
| **Testing Strategy** | **25%** |
| Acknowledgements | 5% |
| References | 5% |

**Competition, Design and Testing Strategy at 25% each is exactly what the SUAS handbook publishes.** The remaining 25% is therefore almost certainly **Formatting 5, Abstract 10, Acknowledgements 5, References 5** — not a mystery category. The website rubric matches too: **Team Information 20% / Vehicle Design and Testing 40% / Website Quality 40%**, identical to SUAS's published split.

### Testing Strategy: the top band hinges on one phrase

Verbatim descriptors, which are the thing to write against:

| Band | Descriptor |
| --- | --- |
| **Outstanding** | "Testing approach is presented in great detail, to include test strategy and plans. Component testing, sensor and control systems testing done **in accordance with a test plan**." |
| Strong | "Detailed testing approach, test strategy, and plans. Documentation shows good overview of components, sensors and control system testing." |
| Average | "Testing approach is presented with sufficient detail, including mention of test strategy and plans." |
| Below Average | "Testing approach is presented with little to no detail. No mention of components or sensors testing." |
| Poor | "Testing is done to a certain degree. **No components and sensors are tested independently. There are no test plans.**" |
| Requirements Not Met | "No mention of testing or connection with the system design." |

**The only difference between Outstanding and Strong is "in accordance with a test plan."** So the report must show a named, dated plan artefact, and component- and sensor-level tests done *independently* of the integrated system. Our [test and verification](test-and-verification.md) gates already are that artefact.

Competition Strategy's prompt is worth quoting too, because it frames the whole report around a trade we face directly: "teams have a limited number of working hours to prepare for the competition; this time could be spent adding additional capabilities **or testing and improving the reliability of an existing capability.**" Its Outstanding band requires "detailed discussion on trade-off studies between system complexity and reliability."

### The optional appendix, and the answer to the page-limit question

**Resolved against the handbook itself, and the answer is the unfavourable one: the SUAS 10-page limit *includes* References and Appendices.** See [handbook 2026 §7](handbook-2026.md#7-design-documentation-with-the-open-question-closed). So the appendix is not free structural space in SUAS, unlike the house-rubric reading below. It is still worth writing, because of the award note, but every page it takes comes off the five scored sections.

The house rubric includes an **optional "Test Plan and Results" appendix**, with this note: "**excellence seen in this section can be eligible for a special judges' award.**" Its specified contents are Scope, Schedule, Resources and Tools, Environment, Risk Management, Results — and the instruction that "any design modifications or changes in competition strategy as a result of testing should be discussed."

**Every Top-TDR winner uses it; the mid-band reports do not.** One competent RoboBoat report maps the testing hierarchy correctly but presents no measured values, no pass/fail log and no appendix — and reads as Strong rather than Outstanding. **The gap between it and the winning report is almost entirely that appendix.**

~~Confirm whether the SUAS 10-page limit excludes appendices.~~ **Confirmed: it does not.** The limit is 10 pages including references and appendices, so plan the appendix as a page budget decision rather than a free extra.

### Formatting is binary

The Formatting criterion has only two bands — Strong, worth all its points, or Requirements Not Met, worth zero. **There is no partial credit for nearly-compliant formatting.** RoboSub's stated format is 8.5 × 11, margins at least 0.8 in, Times New Roman 12 pt, a header on every page with team name and page number, PDF, with two-column IEEE style optional.

Two organiser tips sit inline in the handbook: "It is recommended that papers be peer-reviewed prior to submission," and a link to a university writing-centre guide on abstracts.

### A 2026 change across RoboNation that tells us where this is going

RoboSub and RobotX 2026 both **added a mandatory website content item** that did not exist in 2025:

"**Test event summaries showing time in the field (water time, air time, etc.), objectives, and results. This should include simulations and real-world testing.**"

RobotX also renamed its website criterion from "Vehicle Design Documentation" to **"Vehicle Design and Testing."** RoboBoat, whose handbook published earlier, does not have it. **This is a RoboNation-wide direction, and it is the clearest available signal of where a SUAS website should put its effort.** SUAS 2026 does **not** carry that item, and its website criterion keeps the older name Vehicle Design Documentation, so treat this as a prediction for the 2027 SUAS handbook rather than a current requirement. Note though that the SUAS 2026 Outstanding band already reads "vehicle development **and testing** process is thoroughly documented", and that site search and W3C accessibility are named requirements in the handbook rather than inferences. A public, dated test log with objectives, hours and results scores in two deliverables at once.

---

## 7. What the winning reports actually do

Five patterns, each tied to a rubric line, from reports the organisers themselves name as exemplars.

**Predicted versus measured, with the delta explained, is the most consistently rewarded move in every rubric found.** AIAA DBF's Performance Results criterion says it twice — once for subsystems, once for the whole aircraft. NASA mandates it at CDR and FRR. Formula SAE asks "were discrepancies between predicted and tested results documented and acted upon?" **SAE Aero Design monetises it**: a Payload Prediction Bonus of `max(10 − (FS − PS)², 0)` pays up to 10 points purely for how closely the measured flight score matches the prediction published months earlier.

The best execution found reports **predicted / measured ± uncertainty / sample count / percent delta**, then states the model correction that followed — "it was found that the propulsion model was overpredicting the efficiency… therefore a knockdown factor was applied."

**Record the test conditions or the comparison is worthless.** One winning team: "We also log the conditions during every in-water test run. This allows for thorough review and **ensures the reproducibility of observed behaviors.**" Another re-simulated a flight "matching takeoff weight, propeller, starting battery voltage, cruise throttle setting, and headwind" so the comparison was apples to apples.

**Publish the failures with cause and corrective action.** A winning dated test log, graded Pass / Partial Pass / Fail, includes rows like "Fail — torpedo CO₂ canister exploded, redesign required" and "Pass — 20% force discrepancy found, software updates required", under a stated policy: "**We also wanted transparency by sharing all test outcomes regardless of success or failure.**" It also lists *future* tests with dates and TBD results, which is exactly what the appendix's Schedule bullet asks for.

**Convert test results into competition strategy.** The strongest move found anywhere: a published **confidence table, 0 to 100, per capability** — hydrophone 99, station-keeping 90, perception 65, UAV flight 20, **their own racquetball launcher 5** — sourced from "prior competition results with existing tools and on simulation and on-water test results for new tools," reviewed for consensus by hardware, software and advisors. Those confidences then feed an expected-value equation that ranks task attempt order, and the vehicle can disable a capability whose confidence collapses. **Publishing a 5 out of 100 next to your own mechanism is a credibility move, not a weakness** — and it directly answers the Competition Strategy band about complexity-versus-reliability trade-offs.

**A five-heading template for every test**, from the cleanest report found: **Methodology / Predicted Result / Actual Result / Success Determination / Discussion and Lessons Learned.** It forces a predicted-versus-measured comparison and an explicit verdict on every single test. **Adopt this verbatim as the template for our test reports**, since those reports then serve the TDR, the appendix and the website at once.

### Three pieces of craft guidance worth obeying

From SAE Aero Design's published Written Report Guidelines, which is the most transferable document in the sweep:

- "**The secret to successful writing is simple: organize the report around the scoring rubric, and building an outline that parallels the applicable grading criteria.**" DBF enforces this mechanically: "content that is out of sequence will be treated as missing and scored accordingly."
- "**Build each page around at least one figure. Create the figures first.** Make the figures data-rich, but legible — 9-point font is a minimum size." Note the escape hatch: "the rules do not constrain type font or spacing on figures." On a 10-page IEEE report, a dense table costs a fraction of the equivalent prose.
- **A banned vocabulary.** "**Do not use adjectives to quantify a topic, use data.** Expressions that should not be used: large amount, a few, several, low/high level of, significant increase, many, some, excellent agreement, extensive range. **State a value or range of values, an order of magnitude, or a percentage.**" And: "**it's not a sound engineering decision if it is not supported by data.**"
- "Have the Red Team members read the document **as judges, supplying them with a scoring sheet and a copy of the rules.**"

And one calibration warning, from DBF: "**all reports are assessed relative to each other so that simply addressing each of the sections below may not be sufficient for full credit.**" **Complete is not full marks. The ceiling is set by the field.**

---

## 8. Outstanding

Research was stopped partway to conserve session budget. Still needed:

- ~~Confirm the recovered rubric against SUAS's own handbook wording~~ **Done.** The reconstruction was exactly right: Formatting 5, Abstract 10, Competition Strategy 25, Design Strategy 25, Testing Strategy 25, Acknowledgements 5, References 5, and the band wording matches verbatim. The page limit **includes** appendices. Two instructions we did not have: Design Strategy's Outstanding band turns on the word *creative* and demands calculation steps plus visual aids, and it explicitly says not to include specifications of components not of original design. See [handbook 2026](handbook-2026.md).
- **The 2019 to 2022 SUAS rulebook PDFs**, which carry far more detailed rubrics than the current handbook and are already downloaded (path in [rules watch](rules-watch.md)).
- **IEEE format mechanics** — how the 10 pages are counted and the handbook's exact wording on figures and citations.
- **Per-team numeric website and TDR scores.** `suas-competition.org/2026/rankings` and `/2025/results` both return 404, so the spread is unquantified — it would tell us how much is actually winnable.
- **Transferable rubric evidence** from AIAA Design/Build/Fly, NASA Student Launch, and RoboNation's own RoboSub and RoboBoat journal-paper scoring, which publishes many team papers and a public rubric.
- **The documentation deliverable's own churn.** The 2021 rules scored a Technical Design Paper at 20% with sub-weights Requirements and Acceptance Criteria 10%, System Design 50%, Alternatives Considered 20%, Testing and Evaluation Plan 10%, Safety Risks and Mitigations 10%, plus a separate Flight Readiness Review at 20%. **The 2023 cycle then eliminated the paper entirely and merged it into a video.** It has since returned as the TDR. Worth tracking how stable the current form is.

**Two negative findings worth recording** so nobody chases them: **Georgia Tech has no SUAS team** — absent from the 2026 roster, and the historical Georgia Tech Aerial Robotics competed in IARC with a now-dead site. **Embry-Riddle is not a 2026 entrant** — their SUAS page is frozen on a 2020 aircraft with a live "Covid Info" nav item, and an ERAU news item from May 2026 says their VTOL "has not yet entered full flight testing."

**Downloaded artefacts** are under `C:\Users\prana\AppData\Local\Temp\claude\C--dev\ec1aa0af-bcbd-4649-8bef-370509c84ef7\scratchpad\suas-teams\` — competitor TDRs, AGH's four technical PDFs, and raw HTML captures. **That is a scratch directory and will not survive indefinitely; move anything worth keeping.**
