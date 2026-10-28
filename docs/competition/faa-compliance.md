# FAA and state regulatory compliance

**Status:** Research complete, v1. Every claim below is traceable to the eCFR (Title 14 current issue date 2026-10-22), faa.gov, the Federal Register, the US Code, Texas or Oklahoma statute, or UT Austin's own policy. Where something could not be verified it is listed in section 12 rather than guessed at.

**Scope.** Test flying in Austin through 2028, and competing at Skyway Range, Tulsa. This is the compliance baseline for [the SUAS program](suas-program-plan.md); flight-test gating lives in [test and verification](test-and-verification.md).

---

## 1. The finding that reframes everything: there are two legal pathways

**The SUAS handbook requires a TRUST certificate and never once mentions Part 107.** An exhaustive read of all 52 pages finds zero occurrences of "Part 107", "remote pilot", "pilot in command", "visual line of sight", "BVLOS", "COA", or any airspace class. The single hit for "waiver" is a liability waiver.

That is not an omission. The rules are built around **49 U.S.C. §44809, the exception for limited recreational operations** — and §44809(a)(7) is exactly where the TRUST requirement lives. Section 350 of the 2018 FAA Reauthorization made institutional educational and research use count as a "recreational purpose", which is why a university team can use that route at all.

| | Part 107 | §44809 |
| --- | --- | --- |
| Airman requirement | Remote pilot certificate | **TRUST certificate** |
| Registration | **Per aircraft**, any weight | One certificate covers an inventory; exempt under 0.55 lb |
| Waivers available | **Yes, §107.205 list** | **None — no waiver mechanism exists** |
| Altitude | 400 ft AGL | 400 ft AGL |
| What SUAS inspects | Not mentioned | TRUST, registration, Remote ID |

**Do both.** §44809 is what the competition is built around, but it has no waiver mechanism and it depends on each flight genuinely qualifying as educational. Part 107 is the robust fallback for Austin test flying, it removes any argument about whether a given flight was educational, and it is the only path that preserves waivers.

### Bottom line: the concrete actions

1. **TRUST for every member who may touch a control stick.** Free, online, about ten minutes, all questions correctable to 100%. Required by SUAS §5.3.3 for the Safety Pilot at both safety inspection and the flight line. **Administrators keep no record — download and archive the PDF the day you take it, because losing it means retaking the test.**
2. **Get two to four people Part 107 certificated anyway.** FTN via IACRA, then the UAG knowledge test, then Form 8710-13 and a TSA check. **Recurrent training (ALC-677, free) every 24 calendar months.**
3. **Register each airframe in FAADroneZone — $5, three years — and choose the lane deliberately.** A recreationally-registered aircraft **cannot be flown under Part 107**, and FAA states registration "cannot be transferred between operation types." **Register competition airframes under Part 107.**
4. **Mark the registration number on an external surface**, legible and affixed for the duration of flight. SUAS Appendix B inspects this.
5. **Buy a Remote ID broadcast module that is on the FAA Declaration of Compliance list.** Check before buying — section 4 explains why, and why the list could not be enumerated during this research.
6. **Fly autonomous laps with a dedicated visual observer.** This is the legal architecture, and section 2 is the authority for it.
7. **400 ft AGL and 87 knots are hard ceilings.** At the competition the 150 ft **floor** binds just as hard.
8. **Design the drop to §107.23(b)**, quoted in section 3.
9. **File UT's paperwork first, at least two weeks out.** UT Austin **prohibits** UAV use on University property and on University-sponsored activity off campus except by authorization, and a student who violates it is "subject to disciplinary action, up to and including expulsion." Section 8.
10. **Never overfly DKR-Texas Memorial Stadium.**
11. **Do not plan on BVLOS, and do not pursue a public-aircraft COA.** Sections 6 and 7.

---

## 2. Autonomy is expressly permitted, and the visual observer is the mechanism

This is the clearest answer in the research, and it is the single most important regulatory fact for a fully autonomous competition mission.

**From the Part 107 final rule preamble, 81 FR 42135:** "The FAA agrees with the commenters who pointed out that the ability for a small unmanned aircraft to fly autonomously could add significant utility to a small UAS operation and would further encourage innovation in the industry. **Accordingly, this rule will allow the autonomous flight of small unmanned aircraft.**" The same passage explains the retained duty: because equipage cannot yet substitute for a human pilot's ability to see and avoid, "**this rule will require that the remote pilot in command have the ability to direct the small unmanned aircraft to ensure compliance with the provisions of part 107.**" The accepted commands are enumerated: "the remote pilot may transmit a command for the autonomous aircraft to **climb, descend, land now, proceed to a new waypoint, enter an orbit pattern, or return to home.**"

The same preamble, at 81 FR 42084–85, is explicit that a command link is required "**even if the small unmanned aircraft flight is completely autonomous (i.e., preprogrammed flight operations without further input from the remote pilot)**."

**AC 107-2A ¶5.2.3** (Active; no 107-2B exists) says the same operationally: "During automated flight, flight control inputs are made by components on board the aircraft, not from a CS. If the remote PIC loses the control link to the small unmanned aircraft, the aircraft would continue to fly the programmed mission/return home to land. **During automated flight, the remote PIC must have the ability to change routing/altitude or command the aircraft to land immediately.**"

### The legal architecture for an autonomous lap

**§107.31 is not relaxed by autonomy — but §107.31(b)(2) is the key.** The see-and-avoid ability "must be exercised by either (1) The remote pilot in command and the person manipulating the flight controls … **or (2) A visual observer.**"

So the compliant crew is:

| Role | Duty | Authority |
| --- | --- | --- |
| **RPIC at the ground station** | Monitors telemetry; retains the ability at every instant to command climb, descend, land now, new waypoint, orbit or return to home | §107.19(e), AC 107-2A ¶5.2.3.1 |
| **Visual observer** | Maintains unaided visual contact; scans the airspace for collision hazards | §107.31(b)(2), §107.33(c) |
| **Both** | Effective communication at all times | §107.33(a) |
| **RPIC** | Affirmatively ensures the VO can actually see the aircraft at our lap radius | §107.33(b) |

**This maps exactly onto the SUAS Efficient Operators scoring**, which pays 200 points for two operators and zero for four. Safety Pilot plus GCS Operator is both the maximum-scoring crew and the minimum legal crew.

**Three constraints that follow, and that shape mission design:**

**Lap geometry is bounded by unaided human vision, not by the radio link or the battery.** AC 107-2A ¶5.9 allows brief interruptions — "necessary for the remote PIC to look at the controller to determine remaining battery life" — but states that "**no specific time interval exists in which interruption of VLOS is permissible**, as it would have the effect of potentially allowing a hazardous interruption of the operation."

**Automation does not let one person cover two aircraft.** §107.35 bars manipulating controls or acting as RPIC or VO "in the operation of more than one unmanned aircraft at the same time", and AC 107-2A ¶5.2.3.2 adds: "The use of automation does not allow a person to operate more than one small unmanned aircraft simultaneously."

**Uncertificated students can fly under one certificated RPIC.** §107.12(a)(2) permits it where "the remote pilot in command has the ability to immediately take direct control." AC 107-2A ¶5.2.2 gives three accepted methods: a buddy-box dual control station; the RPIC standing close enough to physically take the control station; or "**an automation system where the remote PIC could immediately engage that system to put the small unmanned aircraft in a pre-programmed 'safe' mode such as a hover, a holding pattern, or 'return home.'**" That third method is a direct description of a QuadPlane with QRTL on a switch.

**Write this into a Concept of Operations**: the VO's position and sector, the command set available to the RPIC at any instant, the lost-VLOS procedure, and the lost-link behaviour. That document is also most of what a §107.31 waiver application would later need, and most of what UT's review group will ask for.

---

## 3. The dropping rule, and the drop-zone constraint that follows

**§107.23 in full:** "No person may: (a) Operate a small unmanned aircraft system in a careless or reckless manner so as to endanger the life or property of another; or **(b) Allow an object to be dropped from a small unmanned aircraft in a manner that creates an undue hazard to persons or property.**"

**Dropping objects is not prohibited.** It is prohibited only in a manner creating an undue hazard. Three other provisions shape a compliant drop:

- **§107.49(e)** — before flight the RPIC must "Ensure that any object attached or carried by the small unmanned aircraft is secure and does not adversely affect the flight characteristics or controllability of the aircraft."
- **§107.39** independently bars operating over any non-participating human being, so the drop zone must be clear of anyone not directly participating.
- **§107.36** — "A small unmanned aircraft may not carry hazardous material." **This is not waivable**, being absent from the §107.205 list.

**So our written drop-zone clearance procedure and our release-mechanism failure analysis are what distinguish a compliant drop from a careless one.** The SUAS rules point the same way: they require the payload to be "safe for humans to be present in the drop area", require a retardant mechanism, and require tool-free separation — which is the organisers' operationalisation of "undue hazard", given that judges may be physically inside the search boundary during drops. See [payload delivery](subsystems/payload-delivery.md).

### Never fly over a non-participant. All four exemptions are closed to us.

§107.39 permits overflight only of a directly participating person, a person under a covered structure or in a stationary vehicle, or an operation meeting a Subpart D category. At 15 lb, every category is closed:

| Category | Requirement | Why it fails |
| --- | --- | --- |
| 1 | **0.55 lb or less** on takeoff including everything on board | 15 lb |
| 2 | Will not transfer **11 foot-pounds** of kinetic energy on impact, plus an FAA-accepted **manufacturer's** declaration of compliance and a permanent label | Energy, and we are not a manufacturer |
| 3 | **25 foot-pounds**, same declaration requirement, and no open-air assemblies | Same |
| 4 | **Airworthiness certificate issued under Part 21** | Not achievable |

**Treat "no flight over any non-participant, ever" as a hard site and design constraint.** §107.145 applies the same logic to people inside moving vehicles.

### The rest of Part 107, in brief

**§107.51** — 87 knots groundspeed; 400 ft AGL unless within a 400 ft radius of a structure and no higher than 400 ft above it; **3 statute miles flight visibility from the control station**; 500 ft below and 2,000 ft horizontally from clouds.

**§107.37** — yield right of way to all aircraft: "the small unmanned aircraft must give way … and may not pass over, under, or ahead of it unless well clear."

**§107.19** — the RPIC "is directly responsible for and is the final authority as to the operation", must ensure no undue hazard "in the event of a loss of control … for any reason", and **cannot be waived**, being absent from the §107.205 list.

**§107.49** — preflight must assess local weather, airspace and flight restrictions, the location of persons and property on the surface, and other ground hazards; brief all participants on operating conditions, emergency and contingency procedures, roles and hazards; and verify the control links and available power.

**§107.9** — report within 10 calendar days any operation causing serious injury, loss of consciousness, or damage to property other than the aircraft above $500 repair or fair market value.

**Two equipment prohibitions that constrain our avionics.** **§107.52** bars operating with a transponder on; **§107.53** bars ADS-B Out in transmit mode. **If our stack includes ADS-B, it must be receive-only** — which it would be anyway, since [the rules watch](rules-watch.md) finds obstacle avoidance was never a sensor task.

Also: §107.13 registration; §107.15 condition for safe operation; §107.17 no participation with an interfering condition; §107.21 in-flight emergency deviation with a written report on request; §107.25 no operation from a moving vehicle except over a sparsely populated area; §107.27 alcohol and drugs; §107.43 no interference with airport traffic patterns; §107.45 prohibited and restricted areas; §107.47 compliance with §§91.137–91.145 and 99.7; §107.7 certificate in physical possession and presented on request to FAA, NTSB, **any federal, state or local law enforcement officer**, or TSA.

---

## 4. Registration and Remote ID

### Registration, Part 48 — and an irreversible choice

**§48.15** exempts the 0.55 lb threshold **only for exclusively recreational operations.** Under Part 107 there is no weight floor: AC 91-57D ¶2.2.8.2 states "any small UA operated under 14 CFR part 107, regardless of weight must have a unique registration number."

**§48.100** (Part 107) registers **each aircraft**; **§48.105** (§44809) issues one certificate covering **all** aircraft in an individual's inventory. FAA: "**Once a drone is registered, its registration cannot be transferred between operation types**", and "**Drones registered under the Exception for Limited Recreational Operations cannot be flown under Part 107.**"

$5 per aircraft, **expires three years after the date of issue**, renewable within the six months preceding the expiry month. Information must be kept accurate and updated **within 14 calendar days** of a change.

**§48.110(a)(7)** requires the broadcast module's serial number on the application, and "must not be listed on more than one Certificate of Aircraft Registration at the same time."

**§48.205 in full:** "(a) The unique identifier must be maintained in a condition that is legible. (b) The unique identifier must be affixed to the small unmanned aircraft by any means necessary to ensure that it will remain affixed for the duration of each operation. **(c) The unique identifier must be legibly displayed on an external surface of the small unmanned aircraft.**"

**Individual or institution?** §48.25(b) requires registration "using the legal name of its owner", and §48.110(a) repeatedly provides for "an applicant other than an individual" acting through an "authorized representative" — so institutional registration is clearly contemplated. **This is a university decision, not an FAA one, and UT has already taken a position:** HOP 8-1070 requires that "all University owned or acquired UAV, weighing more than 0.55 lbs., must be properly registered." **Raise ownership with EHS and Risk Management early.** If the university declines, a student officer must register in their own legal name and own the airframe — which creates a handoff problem at every graduation and an insurance problem immediately.

**Staying under 55 lb matters doubly:** above it, each aircraft registers individually under Part 47, a paper process, and leaves Part 107 entirely.

### Remote ID, Part 89 — and the trap

**§89.105** — no operation after September 16, 2023 unless it meets §89.110 or §89.115. **The grace period is gone:** FAA's enforcement-discretion policy, 88 FR 63518, covered noncompliance "which occurs on or before March 16, 2024", and a Federal Register search finds no extension or successor. **Remote ID is fully enforceable today.**

A student-built airframe cannot meet §89.110, which requires a design or production approval. **Our path is §89.115(a), the broadcast module**, which requires:

- The module's **serial number listed on an FAA-accepted declaration of compliance**
- That serial number **on the aircraft's Certificate of Aircraft Registration**
- Broadcast from takeoff to shutdown, **directly from the unmanned aircraft**
- A pre-flight functional check, and **landing as soon as practicable** if broadcast stops
- **§89.115(a)(2)(ii): "The person manipulating the flight controls of the unmanned aircraft system must be able to see the unmanned aircraft at all times throughout the operation."**

**That last item is the non-obvious finding.** It is a visual-line-of-sight condition embedded in the Remote ID rule itself, separate from §107.31, and it attaches to the person manipulating the flight controls rather than to a visual observer. Two consequences: **a broadcast module can never support a BVLOS operation even with a §107.31 waiver**, and read strictly its VLOS duty cannot be delegated to a VO the way §107.31(b)(2) allows. Harmless for normal laps where the RPIC can see the aircraft anyway; fatal to any future BVLOS ambition, which would need standard Remote ID.

**A module must be on the accepted list** — §89.115(a)(1)(i) requires it in terms, and FAA notes that declarations "may only come from manufacturers." The list is at `https://uasdoc.faa.gov/listDocs?docType=rid&status=accepted`. **It could not be enumerated for this document: the page's backing API returned HTTP 503 on every attempt across the research session, including through a headless browser. Check it yourself and confirm the exact model and firmware against an accepted declaration before purchase.**

What a module broadcasts (§89.315): module serial number, aircraft latitude/longitude, aircraft geometric altitude, velocity, **takeoff** latitude/longitude, **takeoff** geometric altitude, and a UTC time mark. Differences from a standard aircraft (§89.305): a module reports the **takeoff point rather than the live control-station position**, has **no emergency-status element**, and **cannot use a session ID**. Performance (§89.320): position accurate to within 100 ft and altitude within 150 ft at 95% probability, latency no more than 1.0 s, at least one message per second, with self-testing, tamper resistance and error correction.

**§89.125 — ADS-B Out cannot be used to comply with Remote ID.** **§89.120** allows FAA to authorise operations without Remote ID "solely for the purpose of aeronautical research or to show compliance with regulations", but with no published application procedure. Do not plan around it.

### FRIA: a university is directly eligible, and five already exist nearby

**§89.205** limits applications to "(a) A community-based organization recognized by the Administrator. **(b) An educational institution, including primary and secondary educational institutions, trade schools, colleges, and universities.**"

**What it exempts, and what it does not.** §89.115(b) allows flying **without Remote ID equipment** only if both the aircraft and the person manipulating the controls stay inside the FRIA boundary, and that person can see the aircraft at all times. AC 91-57D ¶1.7.4: "**A FRIA does not independently authorize UA operations in a given geographical boundary or airspace.**" It relieves equipment only — not airspace access, not the 400 ft ceiling, not VLOS.

Effective for **48 calendar months**, renewable no later than 120 days before expiry, applied for through FAADroneZone, with AC 89-3 as guidance.

**There are already 13 approved FRIAs in the Austin metro** in FAA's published FRIA layer, of which **five are education-sponsored** — McNeil HS, Stony Point HS, Round Rock HS, Cedar Ridge HS and Akins Early College HS — which proves the §89.205(b) route works in this metro. The rest are AMA club fields, including **Austin Radio Control Association #877** at 8705 Lindell Ln and **Hill Country Aeromodelers #1364** at Mary Moore Searight Metropolitan Park.

**Recommendation: equip a compliant broadcast module.** It costs less than a 48-month FRIA application cycle and it travels with us to Oklahoma. Pursue a UT FRIA only if we want a permanent on-campus or Pickle-campus area where unequipped prototypes can fly during development.

### TRUST, and what it actually covers

Its basis is **§44809(a)(7)**, not Part 107. **A Part 107 certificate holder does not need TRUST for Part 107 operations** — the requirement lives in §44809, §107.1(b)(2) states Part 107 "does not apply to … Any aircraft subject to the provisions of 49 U.S.C. 44809", and Part 107's airman sections contain no reference to TRUST. **The converse is also true and more often misunderstood: TRUST authorises nothing under Part 107.**

**We need it anyway** — SUAS §5.3.3 requires the Safety Pilot to present it at safety inspection and at the flight line, and it is the gating document for any flight we run under the §44809 educational route. Free from every administrator, including the AMA, Embry-Riddle, Pilot Institute and UAV Coach. No stated expiration. **No administrator keeps a record: archive the PDF.**

---

## 5. Night and civil twilight — §107.29

A dawn competition slot puts us in three regimes in sequence:

| Window | Requirement |
| --- | --- |
| Before 30 minutes prior to official sunrise — **night** | **Both** the §107.29(a)(1) training currency **and** anti-collision lighting visible for at least 3 statute miles with a flash rate sufficient to avoid collision |
| From 30 minutes before sunrise to official sunrise — **civil twilight** | **Anti-collision lighting only.** §107.29(b) imposes no training condition |
| At and after official sunrise — **day** | No §107.29 lighting requirement |

The training condition is met by any remote pilot certificated after April 6, 2021 or current on post-April-2021 recurrent training. FAA: "**Recurrent training taken before April 6, 2021 does not meet the requirements** of the Operations Over People rule."

The RPIC may reduce but **may not extinguish** the lighting. **§107.31 VLOS is not relaxed at night or in twilight** — which is precisely why the lighting matters operationally and not only legally. The lighting requirement is waivable under §107.205(b); the training requirement is not. §107.51(c)'s 3 statute mile visibility minimum applies day and night alike.

**Equip compliant anti-collision lighting regardless.** It is cheap, it buys the entire pre-sunrise window, it makes the VO's job materially easier, and a dawn slot with an unlit airframe is a scratched flight.

---

## 6. BVLOS and Part 108: do not plan on it

**There is no final rule.** FAA published a Part 108 NPRM in August 2025; as of today the rulemaking has not concluded. Watch RIN 2120-AL82. The reopened comment period targeted right-of-way and electronic conspicuity, so those provisions are the most likely to change.

Three independent reasons not to build the program around BVLOS:

1. No final rule, and no transition provision for existing §107.31 waivers or §44807 exemptions.
2. **§89.115(a)(2)(ii)** means a broadcast module can never support a BVLOS operation, so BVLOS would also require standard Remote ID, which a student-built airframe cannot obtain.
3. **We do not need it.** The competition flight boundary is about 1,134 m by 1,124 m with a maximum diagonal of 1,400 m — confirmed against the eleven-point polygon the handbook publishes, see [handbook 2026 §2](handbook-2026.md#flight-boundary). Laps are flown inside that box, under a hard 150 to 400 ft AGL band. That is a VLOS problem.

FAA publishes its issued-waiver list and a qualitative trend analysis but no approval rate, and its only stated processing target is 90 days.

---

## 7. The public-aircraft COA route is foreclosed

UT Austin is a public university, so the Certificate of Waiver or Authorization path under 49 U.S.C. §§40102(a)(41) and 40125 looks attractive. **It is not available for student flying.**

**FAA's Office of the Chief Counsel has formally held that "education is not a valid governmental function."** Public aircraft operation status requires the operation to be in furtherance of one of the governmental functions enumerated in §40125(a)(2), and education is not among them.

The one enumerated function that the holding does not foreclose is **aeronautical research** — and whether a student competition team's flying could qualify is the **highest-value written question the club could ask**, directed to the FAA Office of the Chief Counsel, Regulations Division (AGC-200), per AC 00-1.1B ¶8.10. Until someone asks it and gets an answer in writing, assume the COA route is closed.

A useful contrast from FAA's own UAS Test Site page on what designation actually does: test sites, as civil operators, assist clients in "preparing aviation rules waiver and/or airspace authorization material." **Designation helps you get your own authorization; it is not itself a blanket permission.**

---

## 8. Where we can fly, and UT's own policy

### UT Austin HOP 8-1070, effective 2026-05-07 — read this before anything else

**The default is prohibition.** Verbatim: "**The University prohibits the use of a UAV on University property and off campus when conducting University-sponsored activities, except as required by law or pursuant to the authorization process provided by this policy.**"

**It reaches students and off-campus activity.** The policy "applies to all employees, **students**, visitors, University affiliates, and any other individual or entity". A competition trip is a University-sponsored activity off campus. "University Property" includes "buildings, grounds and land that are owned or controlled by means of leases or other formal contractual arrangements" — which covers the **Pickle Research Campus**.

**Required documentation** includes, in the policy's own words, "**educational user exception requirements**, Section 44807 Exemption Documentation, a Certificate of Authorization ('COA') … a UAV registered with the FAA's Unmanned Aircraft Systems Registration system, and a pilot with an FAA Airman Certificate and/or Remote Pilot Certification." **UT's own policy names the educational user exception as an acceptable basis**, which supports the §44809 route provided we document it.

**Approval path.** The **UAV Review Group** — EHS as chair, plus Legal Affairs, University Marketing and Communications, the Office of the President, UTPD and Risk Management — reviews every submission and recommends; the **Director of EHS approves, conditionally approves or denies, and "Decisions … are final."** Criteria include risk to persons and property, distraction from the educational mission, "business, educational or research value to the University", **compliance with UT System insurance requirements**, and legal compliance.

**Lead times and portal.** Submit at `https://utdirect.utexas.edu/apps/csas/uav/request/` **at least two weeks before flight** for university-sponsored operations. **Off UT property, written approval from the property owner must accompany the request.** Contacts: `uavflight@austin.utexas.edu`, (512) 471-8603.

**Sanctions:** "**A student who violates this policy is subject to disciplinary action, up to and including expulsion.**" Also: "The applicant granted approval for use is responsible for any damage resulting from the use of UAV on University property."

**A drafting gap to resolve in writing.** The off-campus clause §VII.A.3 is drafted for "University employees in the scope of employment", while the Scope section sweeps in students conducting University-sponsored activities off campus. **For a student team travelling to a competition, which chain applies is ambiguous on the policy's face. Get EHS to resolve it in writing before the first trip.**

**Practical sequencing.** Build **one standing flight-operations package** — ConOps, site maps, crew roles and VO positions, emergency and lost-link procedures, drop-zone clearance procedure, registration and RPIC and TRUST certificates, Remote ID declaration evidence, insurance confirmation, and a faculty or department memo designating the program an institution-approved academic or research activity — and submit it once with a faculty endorsement. **Ask the review group for a recurring or blanket authorisation for a designated Pickle-campus test area**; the policy's "conditional approval" language is the hook. Do not file a fresh two-week request per flight.

### Austin airspace

**UT main campus and the Pickle Research Campus are both outside controlled airspace** per FAA's current grid data, so **§107.41 is not triggered** and no LAANC authorisation is required there. Verify against B4UFLY and the FAA UAS facility map before each campaign, since grid values change.

**Never overfly DKR-Texas Memorial Stadium.** Both §91.145 via §107.47 and **Tex. Gov't Code §423.0046** apply, and the Fifth Circuit upheld §423.0046 in 2024.

**AMA club fields are the practical alternative for early flying**, and two Austin fields are already FAA-recognised FRIAs (section 4). AMA liability insurance limits and whether coverage extends to autonomous flight, FPV and a payload drop are **unverified** — read the member policy terms.

### Texas law

Tex. Gov't Code Chapter 423 governs UAS imagery and overflight. §423.0046 restricts flight over sports venues; the Fifth Circuit upheld it in 2024. Critical-infrastructure provisions are gated on fencing or signage in the same way Oklahoma's are. **Whether any 2025 legislation amended Chapter 423 is unverified** — the amendment histories read end at Acts 2023.

### Oklahoma law, for the competition trip

**3 O.S. §322** bars operating a UAS over a critical infrastructure facility **below 400 ft AGL**, but the definition is gated: the facility must be "completely enclosed by a fence or other physical barrier … or clearly marked with a sign or signs" indicating entry or UAS flight is forbidden. **The exception that matters is §322(C)(9)**: an operator using the aircraft for a commercial purpose who is FAA-authorised to conduct operations over that airspace.

**A penalty change takes effect 1 November 2026.** Laws 2026, SB 1441 amended §322(D) to route violators to **21 O.S. §1792**, adding criminal exposure where the superseded version carried civil liability alone. Note the two statutes carry **different definitions**: §1792(D)(1)(q) includes airport operational areas, which §322(A)(1) omits.

**21 O.S. §1743** makes it a **misdemeanour** to trespass into airspace within 400 ft AGL with intent to subject anyone to surveillance, to observe someone where they have a reasonable expectation of privacy, or to **intentionally land a drone on another's private property without consent**. The landing provision has the carve-out we would actually rely on: it "shall not apply to the landing of a drone required in the interest of safety or otherwise in an emergency such as to avoid collision with a person or property." **That is the operative defence for an off-field forced landing or a flyaway.**

**3 O.S. §421** establishes the Oklahoma Department of Aerospace and Aeronautics as the state UAS clearinghouse, and exempts university-affiliated research programs from its state-agency registry. **It confers no operational authorisation and does not substitute for FAA permission.**

---

## 9. The competition site: three corrections

**Skyway36 is owned by the Osage Nation, not by Tulsa Ports.** The Osage Nation's official site calls it "Osage Nation's Skyway36 Droneport and Technology Innovation Center", built "in partnership with Osage LLC, Oklahoma State University and Tulsa Innovation Labs". The airpark was signed into federal trust on **11 February 2019**. The official page is `osagellc.com/companies/skyway-36`; `skyway36.com` does not resolve.

**Skyway36 is not an FAA UAS Test Site.** FAA's current page lists **nine** test sites, not seven — two were added in 2026. **The Choctaw Nation of Oklahoma is a test site; Oklahoma State University is not.** OSU is a *partner in Skyway Range*, which is a different thing, and no FAA page links Skyway36 to the Test Site Program. **Do not describe it as an FAA test site** — and note the consequence that Texas's test-site exception would not apply there either.

**RVS is a different airport.** FAA's NFDC records RVS as Tulsa Riverside / Richard Lloyd Jones Jr., **10.67 NM south** of the competition centroid. **The competition site has no FAA airport identifier at all** — the current 28-day NASR subscription contains no landing-facility record there.

### Airspace at the site

Derived from the handbook's 11 published boundary vertices: centroid **36.21646, −96.00818**, extent 1,134 m by 1,124 m, **about 234 acres**. Distance to the KTUL reference point is **5.54 to 6.22 NM**.

| Layer | Geometry | Our position |
| --- | --- | --- |
| Tulsa Class C surface area | 5 NM radius, surface to 4,700 ft MSL | **Outside** (5.54 NM minimum) |
| Tulsa Class C outer area | 10 NM radius, **2,300 ft MSL** to 4,700 | Inside laterally, but far below the floor |
| Tulsa Class E5 | 8 NM radius, from **700 ft AGL** | Inside laterally, below the floor |
| Below 700 ft AGL | — | **Class G** |

The competition ceiling of 400 ft AGL is about 1,090 ft MSL — roughly 1,210 ft below the Class C shelf and 300 ft below the Class E floor. **So competition operations at 150 to 400 ft AGL sit in Class G, §107.41 is not triggered, and no LAANC or ATC authorisation is required at those altitudes.**

**This is a geometric derivation from FAA's legal descriptions, not an FAA determination for the site.** Verify against the FAA UAS facility map grid, B4UFLY and the Tulsa terminal area chart before travel.

Site data worth designing to: field elevation varies **633 to 748 ft MSL** with the runway about 690; **trees to 100 ft AGL inside the boundary**; magnetic deviation 2° west; Runway 1 is 40 ft by 500 ft paved for both VTOL and conventional, Runway 2 is 40 ft by 40 ft paved, VTOL only. Conditions to design for: September Tulsa winds averaging about 14 mph, temperatures to **110 °F**.

The address in the handbook, 951 West 36th St N, is the **contracted event hotel** adjacent to the droneport, not the flight site.

### Nobody holds the authorisation for us

Searches of Skyway Range, Osage LLC, the Osage Nation, RoboNation and the competition site found **no statement of any COA, waiver, §44807 exemption or site authorisation covering visiting operators.** Skyway Range lists "Certification & Regulatory Support" as a *service offered*, which implies help obtaining authorisations rather than coverage conferred.

**The rules put the burden on us explicitly.** §5.2.1: "**Teams demonstrate compliance with all the requirements, to include all required FAA documentation.**" §5.2: power systems "must follow the safety rules and regulations of the host country as well as the team's home country." §5.1 rule 13: "SUAS organizers are not responsible for any damage to a team's UAS as a consequence of participating in the competition."

And no venue exception exists in the regulation. §107.19 pins responsibility on a designated individual and cannot be waived; §44809 is conditional on adhering to **all** its limitations and drops you back into Part 107 if you miss one; FAA states plainly that "it is up to you as a drone pilot to know the rules."

**The competition supplies a safe operating envelope — Class G, a 400 ft ceiling, a 234-acre geofence, mandated failsafes, two-aircraft deconfliction and a documentation gate — well matched to §44809 compliance. It does not supply legal authorisation, and does not claim to. Confirm in writing with RoboNation and Skyway Range before travel** which regime they expect and whether any site authorisation exists.

---

## 10. SUAS requirements that are really engineering specifications

These come from the rules rather than the FAR, but they bind the design as hard:

**Flight termination is defined, and for a fixed-wing VTOL it is specific:** "**Fixed wing: throttle closed, full up elevator, full right rudder, full aileron, and full flaps down.** Rotary: throttle closed." That is a concrete requirement on our mixer and our termination logic, not a philosophy.

**Both the Safety Pilot and the GCS Operator must be able to activate RTL and flight termination independently**, with **automatic RTL after 15 seconds of comms loss** and **automatic flight termination after 90 seconds**. And "safety functionality must be operated using onsite systems with no dependency on any system not under the team's full control" — specifically "cannot have a dependency on the public internet or public cloud providers."

**Altitude 150 to 400 ft AGL, and the floor binds.** Below 150 ft only for takeoff and landing, never below 150 ft over another team's occupied runway, and "stay above 150ft AGL when more than 500ft away from the runway." Out of bounds terminates the mission.

**Flight performance minimums:** waypoint error at most 100 ft with the threshold configured in the autopilot, **turn radius 150 ft**, climb and descent angle 20°, takeoff and landing within about 70 ft by 500 ft.

**Up to two aircraft operate simultaneously** and "teams must prepare their systems to avoid other teams' UAS", though "operations at the competition will not actively put teams in a situation that causes UAS to be on a known collision path."

**Nobody in the prop arc whenever motors can receive power, and "Software based disarm is not sufficient."** Violation may mean disqualification.

**No pieces may depart the aircraft in flight except delivery components.** Battery-electric only, brightly coloured, removable without deconstructing the airframe, no swapping or recharging on the mission clock. All RF must comply with FCC rules in any FCC-allowed band, and **judges provide no spectrum management.**

**GCS display requirements, inspected:** a map with flight boundaries, aircraft position and the waypoint threshold configured under 50 ft; ground speed in **knots**; altitude in **feet AGL**; and the display must be "always viewable by the judges" or no takeoff.

Note that the 2026 handbook was amended five times, most recently on **14 September 2026 — the week of the event — changing Section 3 and Appendix A search boundaries.** Plan for late changes.

---

## 11. The compliance matrix, as actions with owners

| Action | Owner | When | Cost |
| --- | --- | --- | --- |
| TRUST for everyone who may touch a stick; archive every PDF | Safety lead | Now | $0 |
| 2 to 4 people through Part 107: IACRA FTN, UAG test, 8710-13, TSA check | Safety lead | Phase 0 | Test fee per person |
| Part 107 recurrent training ALC-677 every 24 calendar months | Safety lead | Recurring | $0 |
| Decide registration ownership with EHS and Risk Management | President | **Before first registration** | $0 |
| Register each airframe under **Part 107**, mark the number externally | Airframe lead | Per airframe | $5 / 3 yr |
| Verify a broadcast module against the accepted declaration list, then buy | Avionics lead | Before procurement wave 1 | Module cost |
| Add the module serial to the registration certificate | Airframe lead | On registration | $0 |
| Write the ConOps: VO positions, RPIC command set, lost-VLOS and lost-link procedures | Safety lead | Phase 0 | $0 |
| Write the drop-zone clearance procedure to §107.23(b) | Payload lead | Before any drop test | $0 |
| Build and submit the standing UT flight-operations package with a faculty endorsement | President | **At least 2 weeks before first flight** | $0 |
| Ask EHS in writing which approval chain covers students flying off campus | President | Before the 2027 trip | $0 |
| Resolve insurance with UT Risk Management | President | Phase 0 | Unknown |
| Anti-collision lighting visible 3 statute miles | Avionics lead | Before any dawn slot | Low |
| Confirm with RoboNation and Skyway Range in writing which regime they expect | Competition lead | Before travel | $0 |
| Re-run this whole matrix against the 2028 handbook | Safety lead | On publication | $0 |

---

## 12. Open items, where the answer lives

**Equipment and administrative**

- **Which Remote ID broadcast modules are on the accepted declaration list.** The FAA system returned HTTP 503 throughout this research. Re-check `uasdoc.faa.gov` and confirm the exact model and firmware before purchase.
- **The Part 107 knowledge test fee** — set by the testing provider, not published on FAA's process page.
- **Whether FAADroneZone's Part 107 flow permits an organisational registrant**, so UT rather than a student can own the registration. §48.110 clearly contemplates it; confirming the portal requires logging in. Ask the UAS Support Center or UT EHS.
- **AMA liability insurance limits and scope** — specifically whether autonomous flight, FPV and a payload drop are covered. Read the member policy terms.
- **UT's dollar-value insurance requirements for UAS.** HOP 8-1070 defers entirely to Risk Management with no figures published.
- **Whether UT Austin already holds a COA or any Part 107 waiver.** An existing authorisation would change our starting position. Ask EHS; FAA also publishes released COAs and the issued-waiver list.
- **The FAA-recognised community-based organisation list.** The AMA's status is strongly indicated — it administers TRUST and appears as the sponsoring organisation on FAA's own FRIA records — but the formal list page returned 404.

**Site and competition**

- **An authoritative airspace determination for the competition site.** Our Class G conclusion is derived from FAA's legal descriptions plus the handbook's coordinates. Verify with the UAS facility map grid, B4UFLY or ATC Tulsa.
- **Whether anyone holds a site authorisation that extends to visiting operators.** Verified only that none is published. Ask RoboNation and Skyway Range directly.
- **Whether the rules require VLOS in terms.** Zero occurrences of the term; VLOS is a strong inference from the 1,400 m diagonal, the 400 ft ceiling and the TRUST-only paperwork, but it is not stated.
- **Tribal jurisdiction.** The site is Osage Nation land in federal trust since 2019. Whether Osage Nation law alters the applicability of Oklahoma's provisions there is a real gap.
- **The 2027 and 2028 handbooks.** Everything in section 10 is the 2026 ruleset. See [rules watch](rules-watch.md).

**Legal**

- **Whether a student competition team's flying could qualify as "aeronautical research"** under §40125(a)(2) — the one governmental function the education holding does not foreclose. **This is the highest-value written question the club could ask**, to the FAA Office of the Chief Counsel, Regulations Division (AGC-200), per AC 00-1.1B ¶8.10.
- **Whether UT or the UT System is a political subdivision or arm of the State** for §40102(a)(41)(C) purposes. Relevant only if we pursue public aircraft operations; the Texas Attorney General owns the question.
- **Part 108's final-rule date and compliance dates**, and whether the final text retains the proposed accredited-educational-institutions clause. Watch RIN 2120-AL82.
- **Whether any 2025 Texas legislation amended Chapter 423.**
- **How 3 O.S. §322 and 21 O.S. §1792 interact after 1 November 2026**, given their different definitions of critical infrastructure.
