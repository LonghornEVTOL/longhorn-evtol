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

## 6. Outstanding

Research was stopped partway to conserve session budget. Still needed:

- **The published TDR and website rubrics in full** — every criterion, weight and score-band descriptor. The 2026 handbook is thin here; **the 2019 to 2022 rulebook PDFs carry far more detailed rubrics** and are already downloaded (see [rules watch](rules-watch.md) for the path). Also: what the remaining 25% of the TDR rubric is, beyond Competition, Design and Testing Strategy at 25% each.
- **IEEE format mechanics** — how the 10 pages are counted, whether references, figures and appendices are included, and the handbook's exact wording on figures and citations.
- **Per-team numeric website and TDR scores.** `suas-competition.org/2026/rankings` and `/2025/results` both return 404, so the spread is unquantified — it would tell us how much is actually winnable.
- **Transferable rubric evidence** from AIAA Design/Build/Fly, NASA Student Launch, and RoboNation's own RoboSub and RoboBoat journal-paper scoring, which publishes many team papers and a public rubric.
- **The documentation deliverable's own churn.** The 2021 rules scored a Technical Design Paper at 20% with sub-weights Requirements and Acceptance Criteria 10%, System Design 50%, Alternatives Considered 20%, Testing and Evaluation Plan 10%, Safety Risks and Mitigations 10%, plus a separate Flight Readiness Review at 20%. **The 2023 cycle then eliminated the paper entirely and merged it into a video.** It has since returned as the TDR. Worth tracking how stable the current form is.

**Two negative findings worth recording** so nobody chases them: **Georgia Tech has no SUAS team** — absent from the 2026 roster, and the historical Georgia Tech Aerial Robotics competed in IARC with a now-dead site. **Embry-Riddle is not a 2026 entrant** — their SUAS page is frozen on a 2020 aircraft with a live "Covid Info" nav item, and an ERAU news item from May 2026 says their VTOL "has not yet entered full flight testing."

**Downloaded artefacts** are under `C:\Users\prana\AppData\Local\Temp\claude\C--dev\ec1aa0af-bcbd-4649-8bef-370509c84ef7\scratchpad\suas-teams\` — competitor TDRs, AGH's four technical PDFs, and raw HTML captures. **That is a scratch directory and will not survive indefinitely; move anything worth keeping.**
