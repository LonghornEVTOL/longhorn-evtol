# Avionics and datalink

**Subsystem owners:** Avionics lead (radio, power, integration) and Software lead (companion computer, mission logic).

**Status:** Research complete, v1. Flight controller is a Pixhawk 6X running ArduPilot QuadPlane; companion computer a Jetson Orin Nano Super. The buy-the-flight-controller decision and its rationale are in the [program plan](../suas-program-plan.md).

---

## 1. The rule that drives the whole architecture, and it was recently tightened twice

SUAS Appendix B requires **automatic RTL after 15 seconds of comms loss** and **automatic flight termination after 90 seconds**, both activatable by the Safety Pilot *and* the Ground Control Station.

**The 2024 rules were twice as forgiving:** "After 30 seconds of communications loss, the aircraft must automatically RTH or RTL. After 3 minutes of communication loss, the aircraft must automatically terminate flight."

**That change is the design problem.** Under 30 seconds a brief fade was survivable. Under 15 seconds, fast *reconnection* no longer saves you — the link must hold **continuously**, because every "recovers in under a second" product claim is worthless if the gap itself lasts twenty seconds. Section 4 budgets the latency explicitly.

### The termination spec is a concrete requirement, and ArduPilot already implements it

The handbook defines termination: "**Fixed wing: throttle closed, full up elevator, full right rudder, full aileron, and full flaps down.** Rotary: throttle closed."

ArduPilot's `terminate_vehicle()` sets the flap channels to 100%, drives aileron, rudder and elevator to `SERVO_MAX`, throttle to minimum, then disarms. **That is the handbook's list almost item for item, and `AFS_TERM_ACTION = 42` implements the written rule directly.** For a quadplane **43 (QLAND)** is the safer action, but 42 is the literal-compliance option. **Confirm with the judges which they will accept** — it is a question worth asking in writing, not discovering at the inspection table.

### Two rules that close off attractive shortcuts

**No public internet or cloud for anything safety-critical.** §5.3.5: safety-critical functionality "includes, but is not limited to, return to land and flight termination, manual piloting by the Safety Pilot, commanding the autopilot by the GCS Operator, and failsafe for deliveries." **LTE or 5G cannot be our command-and-control link.** Imagery only, if at all.

**Nobody manages the spectrum.** §5.3.12: "Any bands allowed by FCC regulations may be used at competition… **The judges will not provide any RF spectrum management**… Where possible, teams should use frequency hopping or dynamic channel selection. The judges reserve the right to institute RF management if necessary, **but teams may not rely on such.**" The 2024 text was blunter: "any device can be used in any of the allowed bands at any time. **This includes both the flight line and the pits.**" Scale to design against: **64 teams from 10 countries in 2026**, and 508 attendees at the 2023 event.

---

## 2. Link architecture: what every successful team converged on

**Six SUAS technical design papers were retrieved and read. Not one used Herelink. Every one used multiple links on separate bands.**

| Team | Critical telemetry and command | Safety-pilot RC | Imagery |
| --- | --- | --- | --- |
| **UCSD Triton UAS 2022** | **RFD900+, 900 MHz** — "demonstrated performance at 60 km without requiring high gain, directional antennas"; 2 dBi monopole plus 3 dBi dipole "placed orthogonally for polarization diversity" | FrSky X8R, 2.4 GHz | Ubiquiti Rocket AC Lite, 5.8 GHz, **16 dBi ground panel on an antenna tracker** |
| **Technion 2017** | **RFD900, 900 MHz** | Futaba 2.4 GHz — "2.4 GHz is already used for the safety pilot, thus eliminating any chance of interference" | Ubiquiti M5, 5.8 GHz; Arduino tracker on GPS plus RSSI |
| **BYU 2019** | 915 MHz RC and arming | same link | Ubiquiti Bullet M-Series, 5.0 GHz |
| **Monroe CC 2019** | **2 × RFD900x, AES-encrypted** | 2 × FrSky X6R, 2.4 GHz, tested 1.2 mi LOS | Ubiquiti M5, 5.8 GHz, 9 dBi air, **24 dBi ground with a human aimer** |
| **Notre Dame** | **TBS Crossfire Mini 915 MHz plus RFD900+** | Crossfire | LTE over cloud VPN — "so we do not have to take on the complexity involved with setting up directional antennas" |
| **UCF 2018–19** | **RFD900x** — "offers more range than the high-speed data link, to be used for critical telemetry and control" | — | Ubiquiti Rocket M5 with omnis |

**Decision: critical command and telemetry go on 900 MHz.** RFD900x gives 30 dBm transmit and −101 dBm sensitivity at its default 64 kbps, in a band with 8.3 dB less free-space path loss than 2.4 GHz. Herelink gives 23 dBm (200 mW in the USA; the 20 km figure is a regulatory ceiling, not an operating spec) and −99 dBm.

**Herelink's measured field range is 3 to 4 km** — "only 3-4km of telemetry and video before there is connection loss" at 100–120 m AGL over rural terrain with stock antennas, where antenna orientation made "not much difference." A ground-level test produced **609 m**. The 16 km figure in circulation was "tested over water, so arguably perfect conditions," with the same source noting that "in a more typical land environment… generally single digit kilometers." Measured latency is 200–280 ms indoors at short range, against a 110 ms claim.

**So Herelink lands inside our requirement rather than beyond it, and it inverts the architecture those six teams each arrived at independently** — it puts command, telemetry *and* video into 2.4 GHz, the one band those teams deliberately quarantine for the safety pilot, and the band the rules single out as needing hopping and directional antennas precisely because nobody is coordinating it among 64 teams.

Two further Herelink constraints: the controller antenna connector is **Mini BNC**, which limits swapping in a higher-gain or tracked ground antenna — our main range lever. And one air unit binds to one ground unit, so **no pre-bound spare airframe**, which conflicts with wanting a spare at Tulsa.

### Two RFD products that solve the co-site problem architecturally

**RFD VideoLink** — 900–928 MHz, 1 W, H.264 to 720p, sensitivity better than −101 dBm at 64 kbps, AES-256, "Integrated Telemetry + Video Link over MAVLink" with PPM/SBUS passthrough, "ranges exceeding 5 km."

**RFD TxMod / TXMOD V2** — a JR-bay module carrying the RC transmitter's PPM/SBUS **and** MAVLink over the same RFD900x link. Caveat: its built-in Wi-Fi uses a PCB trace antenna with no external port, which puts a 2.4 GHz emitter inside the ground station.

**Together these put the entire link stack on one 900 MHz band with one airborne antenna pair. That is the single largest co-site risk reduction available to us** — and the exact inverse of the Herelink approach. Section 5 explains why reducing the number of airborne transmitters beats any amount of physical separation.

### ExpressLRS is the RC link, because it is the only option that publishes what a link budget needs

Current release 4.1.0, July 2026, actively maintained. Sensitivity per packet rate, cross-checked between the documentation and the firmware source:

| 900 MHz | Sensitivity | 2.4 GHz | Sensitivity |
| --- | ---: | --- | ---: |
| 25 Hz | **−123 dBm** | 50 Hz | −115 dBm |
| 50 Hz | −120 | 150 Hz | −112 |
| 100 Hz | −117 | 250 Hz | −108 |
| **200 Hz** | **−112** | 333 Hz Full / 500 Hz | −105 |
| 250 Hz | −111 | F1000 | −104 |

At matched rates, 915 MHz buys 4–5 dB of sensitivity **plus** 8.55 dB of path loss — about 12.5 to 13.6 dB net, a 4.2 to 4.8× theoretical range ratio at equal power and gain.

Worked budget at 5 km, 100 mW, 2 dBi each end: **2.4 GHz** gives −90.2 dBm received, so about **18 dB of margin** against 250 Hz; **915 MHz** gives −81.7 dBm, so about **30 dB against 200 Hz**. ELRS's own documentation states our exact requirement: "**5km is achievable on a ceramic antenna receiver with no LNA/PA at 250Hz/100mW with a clear line of sight.**" Their public leaderboard, which carries DVR video for every entry and flags whether each flight failsafed, includes **35.0 km at 250 Hz and 100 mW** on 2.4 GHz and **5.0 km at 200 Hz and 50 mW** on 900 MHz.

**Two legal limits to respect.** FCC §15.247(b)(3) caps digital modulation at **1 W**, so **ELRS's 2000 mW setting is 3 dB over the limit**. And (b)(4) requires dB-for-dB conducted power reduction above **6 dBi** of antenna gain, with the point-to-point exception explicitly denied to omnidirectional use — so at a 9 dBi ground antenna we drop to 500 mW.

**Gemini does not buy range, and ELRS says so:** "**Will Gemini increase my range? No.** The packet sensitivity remains the same for the RF Mode… Gemini will help maintain a higher LQ to the point of failsafe. **However failsafe will still happen at approximately the same distance.**" It buys interference diversity and link-quality stability. A subtlety: in MAVLink mode "the two radios send different halves of the telemetry stream instead of redundant copies, doubling the downlink" — redundancy on the uplink, throughput on the downlink.

ELRS MAVLink mode needs **ESP-based transmitter and receiver hardware; STM32 cannot run it**, the GCS side is fixed at 460800 baud, and the telemetry ratio is forced to 1:2. **Parameter conflict to resolve ourselves:** ELRS says `SERIALx_BAUD = 460` while ArduPilot's own CRSF page says 115. Trust ELRS for ELRS hardware. Two documented CRSF gotchas with real reliability consequence: "Rebooting the autopilot via MAVLink will cause loss of communication with the CRSF receiver until the receiver is power-cycled," and "The CRSF TX must be transmitting before the receiver powers up."

### Two options ruled out

**TBS Crossfire is disqualified by one behaviour.** The manual states that in the serial bridge, "**As soon as the Crossfire switches to 50Hz mode, the transmission will be stopped**", and "This will only work while your Crossfire is in the 150hz mode. As soon as the link switches to 50Hz, the Bluetooth module stops sending serial data." **Its dynamic profile protects the RC link by dropping to 50 Hz at range — and kills the MAVLink path exactly when we are furthest out.** Forcing 150 Hz keeps telemetry but, per TBS, limits range. Crossfire also publishes no failsafe timing in milliseconds and no telemetry throughput figures.

**Dragonlink is dead.** `dragonlinkrc.com` resolves but refuses every HTTP connection; the Internet Archive's last capture of any kind is April 2025; resellers list it "Discontinued" or under a `/discontinued/` path. No sensitivity figure is published by anyone, including ArduPilot's own page. And it is 433 MHz, which in the US is amateur-band only — "Operating this equipment in the US requires an amateur radio license" — a licensing problem for SUAS independent of the hardware.

**FrSky R9 and ImmersionRC Ghost publish no sensitivity figure at all.** We cannot defend a link budget for a radio whose sensitivity the manufacturer will not publish.

---

## 3. Video, if we carry it

**Prefer the DJI O4 Air Unit Pro over the O3, for a non-obvious reason.** The O3 is "2.400–2.4835 GHz **RX only**" plus 5.8 GHz transmit — so dual-band does not mean the downlink can move off 5.8 GHz, and it leaves a live 2.4 GHz receive front end that a co-located 100–1000 mW ELRS transmitter will sit on top of. **The O4 has no 2.4 GHz at all** (5.170–5.250 and 5.725–5.850 GHz), 15 ms latency, 15.6 g, and leaves the entire 2.4 GHz band free for our control link.

Neither can replace our telemetry link. **There is no MAVLink tunnel on O3 or O4** — only MSP DisplayPort OSD, reportedly working at `OSD_TYPE = 5`, `SERIALn_PROTOCOL = 42`, `MSP_OPTIONS = 4`.

**Never 2.4 GHz video.** It exists, up to 800 mW, and it is the worst available choice: co-channel with our control link, and **no filter separates a co-channel interferer**.

**Ground antenna is the binding constraint on video range.** At DJI's own 33 dBm EIRP, free-space loss at 5.8 GHz is 111.8 dB at 1.6 km and 121.7 dB at 5 km. With a 10 dBic ground patch that is −68.8 and −78.7 dBm; **with stock goggle omnis at about 2 dBi it is −86.7 dBm at 5 km, about 9 dB worse** — most of a digital system's remaining margin. Subtract 3 dB more for a linear air antenna into a circular ground antenna, and 0.5 to 1 dB per right-angle SMA adapter. **One aimed 10 dBic patch plus one omni on a diversity receiver is the minimum for three miles; an omni-only ground station will not hold 5 km.**

Analog degrades gracefully where digital cliffs: analog FM carries the picture in the carrier, so falling carrier-to-noise costs a proportional slice of quality and a human can fly on a picture that is 80% noise. A digital link must decode a block to show anything, so below the required energy-per-bit the FEC fails and the decoder outputs nothing — compressing usable to unusable into a few dB. **No measured carrier-to-noise-versus-quality curve exists for any of these systems**, which is itself worth knowing before betting a mission on one.

---

## 4. Budgeting the 15-second rule

ExpressLRS receiver disconnect timeouts, extracted from the firmware's `DisconnectTimeoutMs` field:

| Rate | Declares disconnect | Re-lock |
| --- | ---: | ---: |
| 2.4 GHz FLRC / 500 Hz / 333 Hz Full | 2500 ms | 2500 ms |
| 2.4 GHz 250 Hz; 900 MHz 200 Hz | 3000 ms | 2500 ms |
| 2.4 GHz 150 Hz; 900 MHz 100/250 Hz | 3500 ms | 2500 ms |
| 50 Hz either band | 4000 ms | 2500 ms |
| **900 MHz 25 Hz** | **6000 ms** | **4000 ms** |

**Worst-case chain:** about 3 s for the receiver to declare disconnect, plus ArduPilot's `RC_FS_TIMEOUT` at 1.0 s, plus `FS_LONG_TIMEOUT` at 5 s, plus re-acquisition — **roughly 9.5 s against a 15 s requirement, leaving about 5.5 s of margin.**

**Avoid 25 Hz on 900 MHz**: 6 s to detect plus 4 s to re-lock consumes the budget before ArduPilot has acted. And note that lowering `AIR_SPEED` on an RFD900x buys range but **lengthens TDM sync time**, which is a direct hit on reconnect latency against the same rule.

### Tune ArduPilot's telemetry demand down, not the radio's rate up

Measured: ArduPilot Plane defaults at 4 Hz streams demand **3.5 kB/s disarmed and 4.0 kB/s armed** — "on a typical 57600 baud RF telemetry radio, this equates to **59%–69%** of the available radio datarate." All streams at 1 Hz: 0.9 to 1.0 kB/s. `MAVLinkHL` with HIGH_LATENCY2 at 0.2 Hz: about 0.01 kB/s.

**Dropping streams from 4 Hz to 1 Hz cuts demand 4× for free, while every step up in ELRS packet rate costs 3 to 4 dB of sensitivity.** Against the ELRS MAVLink throughput table, default streams clear only F1000 or Gemini; 1 Hz streams clear 333 Hz Full on 2.4 GHz but **nothing in legacy 900 MHz**, where 200 Hz carries only about 470 B/s. Against an RFD900x, basic MAVLink needs 12 to 14 kbps against 43 kbps total at the 64 kbps default — comfortable, which is the quantitative reason the 900 MHz modem is the right home for the critical link.

Watch one indexing trap: `SRx` parameter indices map to the **order of MAVLink-active ports**, not to `SERIALx` numbers. And in ArduPilot 4.7 and later these parameters are renamed **`MAVn_*`**, not `SRn_*`, with a conversion script in tree.

### Diagnostics, so this is measured rather than assumed

- **`signal_dBm = (RSSI / 1.9) − 127`** for SiK and RFD radios.
- **Fade margin is approximately (RSSI − noise) / 2 dB**, and "**every 6 dB of fade margin roughly doubles range.**" The link is lost when the RSSI and noise lines meet.
- **Error correction is no longer recommended** — the 12/24 Golay code halves available bandwidth and reduces range.
- **`MAVLINK = 2`** plus `MAX_WINDOW` 131 gives 33 ms latency at the cost of maximum bandwidth; both ends must match.
- **A gotcha that will waste an afternoon:** RSSI and noise are reported as positive numbers when the actual measurements are negative, "which can give the false impression that the noise is higher than the signal."
- **Check radio temperature before blaming interference.** RFD's firmware "will reduce the operating duty cycle" above a safe temperature, costing throughput. Read it with `ATI7`. One documented case had RSSI collapsing to near zero about a dozen times from a ground unit in a sealed black case at 90 °F. **Tulsa in September is exactly this failure.**

---

## 5. Co-site interference: separation cannot save us

Five mechanisms, with the level at which each bites:

| Mechanism | Typical level |
| --- | --- |
| Fundamental overload, receiver front-end saturation | **−10 to +20 dBm at the receiver input** |
| Transmitter noise sidebands in the victim passband | −120 to −80 dBm |
| Passive intermodulation from nonlinear metal joints | −160 to −100 dBc |
| Transmitter harmonics | −60 to −30 dBc |

Applied to a 1.5 m airframe, free-space coupling:

| Interferer | Separation | Power arriving at another antenna |
| --- | --- | ---: |
| 5.8 GHz video at 33 dBm EIRP | 15 cm | **+1.8 dBm** |
| same | 30 cm | −4.3 dBm |
| same | 1 m | −14.7 dBm |
| 915 MHz telemetry at 30 dBm | 1 m | −1.7 dBm |

Against receivers at −101 to −123 dBm, **the carrier-to-sensitivity ratio the front ends must survive is 105 to 130 dB. Nothing about physical separation on a 15 lb airframe delivers that.** The receivers' band-select filters and the transmitters' own out-of-band noise floors do the real work.

**So separation is a second-order lever. Reducing the number of airborne transmitters is the primary one** — which is the argument for VideoLink and TxMod in section 2.

**The best-quantified real case**, a 400 mW video transmitter at 1280 MHz with at least 300 mm of separation:

| Test | Video TX on | TX off | Loss |
| --- | ---: | ---: | ---: |
| Ground range check | 800 m | 850 m | −6% |
| Low-power range check, normal aim | **85 m** | 101 m | **−16%** |
| Same, antenna aimed at the receiver | 68 m | 72 m | −6% |
| Same plus a low-pass filter | **63 m** | — | **the filter made it worse** |

**A 6 to 16% range loss from a 400 mW transmitter at 300 mm, whose second harmonic falls outside the victim band, is the realistic baseline.** An unfiltered 1 W 5.8 GHz transmitter 10 cm from an ELRS antenna is far more aggressive. And note the filter made things worse — insertion loss is real, and filters are a last resort.

**Acceptance criterion to test against:** 3GPP TR 37.941 allows **0.8 dB of victim desensitisation**, defined as interference 7 dB below the victim's noise floor. Log RSSI and noise before and after every change at a fixed distance. **FCC compliance guarantees nothing about co-site survival.**

---

## 6. Antennas, and the quadplane-specific finding

**Measured polarization penalty, between two parking garages 600 m apart with near-clear line of sight:**

| Antenna 1 | Antenna 2 | Received | Throughput |
| --- | --- | ---: | ---: |
| Vertical | Vertical | −64 dBm | 40 Mb/s |
| Horizontal | Vertical | **−78 dBm** | 15 Mb/s |
| Horizontal | Horizontal | **−93 dBm** | **0 Mb/s** |

**This is the single most directly applicable dataset for a quadplane.** A 90° attitude change cost **14 dB and 62% of throughput**; full cross-polarization cost **29 dB and the entire link**. **A quadplane rotates its airframe, and its antennas, through exactly this range at every transition.** ArduPilot states the consequence plainly: "Airborne antennas can lose signal when the aircraft rolls, so a simple wire antenna may show dips in RSSI during turns."

**So use circular polarization, or orthogonal polarization diversity.** RFD900x has two ports with automatic per-packet diversity switching: "Testing by Silicon Labs has shown that link budgets can be improved up to the order of **8dB** by employing a diversity scheme," with spatial diversity at 25 cm minimum, and polarization diversity working "provided that the two antennas in use are mounted at 90 degrees to each other" — which "can significantly improve performance when orientation of the antennas is variable, e.g. on manoeuvring aircraft." **Do not terminate an unused port with a short or an RF load**; that breaks the diversity feature, while operating with no antenna fitted will not damage the modem.

**Our worst case is a banked turn at maximum range with the fuselage between the antenna and the ground station** — pattern null, polarization mismatch and airframe shadowing stacked at once. The design calls for 30° of bank at cruise, so this is not hypothetical.

**No antenna inside or behind carbon fibre, ever.** A UAS antenna study concluded "the greatest signal attenuation in each case was caused by the carbon due its conductivity." The Technion team kept a monopole over a dipole specifically to reduce coupling with the conductive fuselage, and documented that **the landing gear itself created interference.**

**There is a cheap original experiment here for the technical design report:** a two-antenna S21 measurement on an offcut of our own layup at 915 MHz, 2.4 GHz and 5.8 GHz. **No published measurement exists at those frequencies at drone-typical thickness**, so measuring ours would be a genuine contribution and would directly justify our antenna placement.

### Buy mast height before antenna gain

- Measured at NCSU: for UAVs "two-ray ground reflection is assumed to be the main source of fast fading", with "almost **30 dB variation in signal strength** even when the receiver is near the transmitter."
- A published two-ray UAV datalink model at 900 MHz and 1 W shows spatial oscillations from periodic constructive and destructive interference, concluding that "**a more reliable link should employ, if possible, a diversity scheme to guarantee a more stable performance over the complete flight.**"
- ArduPilot: keep the ground antenna "a couple of meters off the ground." One operator raised theirs to about 4 m on a tripod specifically to clear the Fresnel zone, and reported that corn fields "act like sponge for the RF signal."
- Doubling antenna height adds about 6 dB at larger distances.

**At 3 km the first Fresnel zone radius at 2.4 GHz is about 10 m, so a ground antenna at human height is partially obstructed regardless of its gain.** Combined with 30 dB of two-ray fading, **a low tripod can put us in a 20 to 30 dB null at a specific range and bearing while the link budget says we have margin. That is the most likely single explanation for a link that works at 3 km one day and dies at 1 km the next.**

### The tracking problem, and how the teams actually solved it

**Four of six teams either built an antenna tracker or dedicated a person to aiming.** Triton UAS used a 16 dBi panel on an automatic tracker, noting that "due to the high directivity of the antenna, it is vital that the plane is kept within the area of peak gain." Monroe CC used a 24 dBi directional and "one team member is assigned to manually track the UAV." Technion built an Arduino tracker on GPS plus RSSI specifically to "avoid RF radiation and low accuracy caused by the manual tracking" and to free up a crew member. Notre Dame ran ArduPilot's AntennaTracker. BYU's FMEA mitigation for "Loss of Connection" was literally "Assign antenna pointer."

**That is in direct tension with the two-operator crew that scores 200 points.** A 13 dBic patch has a 24° vertical beam; on a small portable mount a 45° misalignment loses more than the gain won. **The defensible compromise is a moderate-gain patch at 9 to 10 dBic with a 68° beam on one diversity port and an omni on the other, on a mast** — tolerant of aiming error, legal under the §15.247(b)(4) gain limit, and it buys the Fresnel clearance that gain alone cannot.

Also note: an RFD900 at about 1 W "is at or above legally allowed transmission power", so **a high-gain antenna on transmit exceeds the EIRP limit in the peak direction.**

---

## 7. Documented link failures worth designing against

**No SUAS technical design paper narrates a lost-link incident.** Six were retrieved and searched for incident language; only risk-table entries surfaced. The absence is structural and worth stating in our own report: the TDR is a scored, pre-mission deliverable, so teams write design justification and never post-mortems. **The real incident data lives in the ArduPilot forum.**

**The most transferable lesson, from a crash: do not map arm/disarm or mode selection to SBUS channels on a digital link.** One operator running Herelink telemetry plus RCIN over SBUS saw data loss about 20 seconds after takeoff, radio error 2, failsafe, RTL — and then the aircraft **disarmed mid-landing and crashed**, apparently because when failsafe cleared, channel 7 (the arm/disarm button on the controller) reset to a low value. Herelink's own manual advises against assigning autopilot mode selection to SBUS channels, recommending MAVLink commands instead. A university team's own incident report shows the same pattern from another angle: failsafe was set to RTL and worked in ground tests, but **after a controller reboot the arming switch was flagged and the aircraft disarmed at about 30 m.** **With a 15 second trigger we will exercise this path routinely.**

**"No RC receiver" that would not clear.** Another operator tested radio failsafe twice pre-flight; after the second test telemetry returned but RC did not, and the fault **stayed up** — it did not clear on return with good telemetry and a 20 m line-of-sight flyby. They then lost telemetry and RC several times **from about 1.5 km out, inside our requirement**. Aborted via Loiter and RTL from the ground station. No root cause identified.

**Telemetry dead at 150 m with a video transmitter aboard.** An RFD900 pair with 3 dB ground antennas dropped beyond 150 m while an 800 mW 1.3 GHz video link and the RC control held fine for a 35-minute flight. Converting the reported RSSI gives about −74 dBm where free space predicts about −50 dBm — **24 to 34 dB below prediction, the signature of a detuned, blocked or badly matched antenna rather than a noise problem.** ArduPilot's lead developer, on a similar thread about a lost aircraft, identified the 800 mW analog video transmitter as the likely culprit, explaining that **analog FPV transmitters are not low-interference devices and blanket a wide range of spectrum**, and advising a test with the transmitter removed.

**A cracked PCB joint at the antenna connector.** Intermittent then total loss about 20 seconds into a flight; one user found no connection between the antenna connector and the IC, with bending the board restoring contact. Exactly the failure RFD's **0.5 Nm torque / 500 mating cycle / vibration** warnings predict. **Use pigtails so the airframe antenna load never hangs off the modem connector, and count mating cycles** — a competition means repeated assembly.

**A university team's own measured collapse, from kilometres of claimed capability to 425 m.** Logged every 50 m, link quality fell off a cliff at 150 m. Their own diagnosis: "the stationary antenna was propped up on top of a metal picnic table… there were probably Fresnel zone infractions," and "the antenna was just propped up, not held vertically." Their conclusion: "the sharp drop indicates that the test was not done in an environment like free space." The same team measured **66% packet loss at 300 m with non-monotonic RSSI — worse at 50 m than at 100 m**, which is textbook two-ray multipath at ground level. **Any range test run from ground level will produce this, and it will not predict flight.**

**A competition team that lost an aircraft to lost comms.** ETS Dronolab at the 14th Canadian UAS Competition placed first in the first phase, then on the large terrain "many teams [lost] communications with their drones including us", and "our team lost its drone in the forest due to this." **The important detail is that it was not one team's installation error — many teams lost comms on the same site on the same day.** That is a propagation, terrain and congestion signature, and it is precisely the scenario the no-spectrum-management rule sets up.

**Two corrections to earlier briefings.** The lost-aircraft incident belongs to **ETS Dronolab, not Polytechnique Montréal.** And **the UT Austin range-collapse story could not be corroborated anywhere** — no UT Austin technical design paper was located, and no public mention of RFD900, Herelink or a range incident; only that the team placed 6th of 69 at SUAS 2018. **If that story is institutional memory, it is the one piece of evidence we hold that the public record does not. Write it down, and ask Dr. Sirohi and Texas Aerial Robotics.**

---

## 8. Companion computer integration

### The finding that invalidates the most-copied pattern

**On ArduPilot Plane and QuadPlane, `SET_POSITION_TARGET_GLOBAL_INT` does not command a lat/lon position. It commands altitude only.** In `GCS_MAVLINK_Plane::handle_set_position_target_global_int()`, latitude and longitude are **hardcoded to `0`** and discarded; only the altitude field is used. The wiki says the same in prose: "**No other fields in position target messages are currently supported. Use movement commands to send a lat lon alt target.**" The handler also returns early unless the vehicle is in GUIDED, with an in-source comment explaining this is "to avoid inadvertently sending these kinds of commands when the autopilot is responding to problems in modes such as RTL, CIRCLE, etc."

**Why this matters to us specifically:** the most widely copied student-team pattern is publishing a global position target to `/mavros/setpoint_raw/global`. **That is a Copter pattern. On our QuadPlane it will silently do nothing but change altitude.** At least one surveyed team's code does exactly this — and their aircraft is a multirotor, so it works for them and will not work for us.

**Use one of these instead:**

| Method | Details |
| --- | --- |
| **`MAV_CMD_DO_REPOSITION` (192)** in a `COMMAND_INT` | Decodes the full location, sanitises it, **rejects the target if it is outside the fence** (logged as `DEST_OUTSIDE_FENCE`), switches to GUIDED if the change-mode flag is set, then sets the guided waypoint. `param3` is loiter radius in metres, `param4` loiter direction |
| **`MISSION_ITEM_INT` with `current = 2`** | The guided-goto form: `command = MAV_CMD_NAV_WAYPOINT (16)`, `current = 2`, frame 0 or 3 for AMSL / 6 for alt-above-home / 11 for terrain, lat and lon as degrees × 1e7 |

Cornell's quadplane team converged independently on the same answer, documenting "`POST /jump` sends GUIDED plus `DO_REPOSITION`."

**Always use the `_INT` message variants.** `MISSION_ITEM` carries lat/lon as float32, which quantises to metres or worse at full latitude — unusable against a 50 ft scoring radius. `MISSION_ITEM_INT` uses int32 in degrees × 1e7, about 1.1 cm.

### `Q_GUIDED_MODE` is the decision to write on the airframe card

**The default is 0, disabled.** The parameter description: "This enables use of VTOL in guided mode. When enabled the aircraft will switch to VTOL flight when the guided destination is reached and hover at the destination." The gating function adds constraints the description omits: it returns false unless the vehicle is in AUTO or GUIDED, and false in AUTO on a loiter-turns item because "loiter turns is a fixed wing only operation."

| Setting | Behaviour at the destination | Consequence for delivery |
| --- | --- | --- |
| **0** | Flies and loiters as a **fixed wing** | Release while circling at about 25 m/s — needs a forward-throw *and* wind model |
| **1** | **Transitions to VTOL and hovers** | Release from a station-keeping hover, simpler ballistics, costs battery and transition time |

Cornell flies `Q_GUIDED_MODE 1` with `Q_ASSIST_SPEED 18`. Budget for the documented pitch-up and altitude gain during deceleration into a VTOL hold. See [payload delivery](payload-delivery.md), which recommends a slow translating release rather than a true hover.

Also: **`GUIDED_TIMEOUT` defaults to 3.0 s** — if an external guided command stops arriving, the vehicle reverts to regular GUIDED. GUIDED "fly to" uses the same L1 controller as AUTO; the `GUIDED_P/I/D` gains apply only while a change-heading target is active. And **a geofence breach puts the aircraft into GUIDED heading for the fence return point, so GUIDED is not exclusively ours** — our software must tolerate finding itself in GUIDED for a reason it did not cause.

### Library choice

| Option | Verdict |
| --- | --- |
| **MAVROS on ROS 2** | **Recommended backbone.** Actively maintained (2.16.0, September 2026), binaries for Humble, Jazzy, Kilted and Rolling — **none for Iron**. The only option with mission upload and download as first-class services, plus arbitrary MAVLink via `/mavros/cmd/command`, and stream-rate services |
| **pymavlink** | The fallback for every command the higher-level library gets wrong, and on a QuadPlane that will be several. No state model, no reconnection, nothing for free |
| **MAVSDK** | Best-maintained, worst-matched to ArduPilot Plane. Its own tracking issue says "Some, but not all MAVSDK plugins have ArduPilot support… If not listed, the state is unknown." The high-level `Mission` plugin is listed as broken against ArduPilot. Open bugs include flight mode returning UNKNOWN for many ArduPilot modes and `goto_location()` returning command-denied — both directly in our mission path |
| **DroneKit** | **Unmaintained.** Last commit May 2024, 450 open issues, a README banner asking for maintainers, and the latest PyPI release is from **March 2019**. One surveyed team ships a patched wheel from their own private index because it will not install on modern Python. Do not start new work on it |
| **ArduPilot native DDS** | **Not viable as the primary interface.** Humble only, experimental ABI, rates capped by `SCHED_LOOP_RATE` which is 50 Hz for Plane, and — decisively — **no mission upload or download interface and no servo command at all.** An ArduPilot DDS maintainer's own assessment: "MAVSDK currently has a more rich feature support than the ROS 2 support in ArduPilot" |

### Architecture: upload missions, do not stream setpoints

The surveyed teams split cleanly, and the split maps onto a real trade-off.

**Mission upload in AUTO** — the autopilot owns navigation. Compute target coordinates, splice them into the live mission, let ArduPilot fly. It **survives a companion crash, a companion reboot and link loss**, because the mission is in the flight controller's memory; the RTL item stays at the end; and `Q_GUIDED_MODE` becomes irrelevant because AUTO handles VTOL waypoints natively. Costs: round-trip latency per re-plan, and ArduPilot rate-limits streams "4 or more times slower" *during* the upload.

**GUIDED setpoint streaming** — the companion owns navigation. Immediate response and continuous re-planning, but `GUIDED_TIMEOUT` means a companion stall reverts to the last position, link loss means no plan, **and on a QuadPlane the obvious primitive does not work.**

**The most instructive data point: the team whose hardware most closely matches ours tried the hybrid and backed out.** Their README documents that "the GUIDED hold ('hold in GUIDED until every image has been processed') is commented out," and "the servo release and the GUIDED hold step are in the code but commented out." They shipped pure mission-splicing in AUTO.

**Decision: waypoint laps and the mapping run as an uploaded AUTO mission; delivery waypoints spliced in after detection.** Reserve GUIDED with `DO_REPOSITION` for holding over a target while vision converges.

**Returning to AUTO cleanly needs `MAV_CMD_DO_SET_MISSION_CURRENT`**, which clears the next-waypoint crosstrack flag and resumes a stopped mission. Without it the aircraft resumes at whatever item it was on and may crosstrack back to a leg already flown.

### Payload release commands, and the traps

**Use `MAV_CMD_DO_SET_SERVO` (183)**, which is what every surveyed team ships. `param1` is the servo instance, `param2` the PWM. The gripper abstraction supports only **one** gripper, so two payloads cannot both use it.

Three traps, all documented, two of which cost 100 points each:

- **`SERVOn_FUNCTION = 0` leaves the output unassigned, and an unassigned output emits `SERVOn_TRIM` as soon as board safety is cleared.** Set trim to the grab position.
- **`FS_OPTIONS` contains a bit that releases the gripper on failsafe** — a configuration in which a radio failsafe intentionally drops the payload. Verify it is disabled.
- **`DO_SET_SERVO` acknowledges the command, not the drop.** Add a microswitch or photointerrupter, or at minimum log `SERVO_OUTPUT_RAW`. One surveyed team's winch reports a six-state status gated Complete before RTL; that is the right instinct.

Also: AUX outputs run at `SERVO_RATE`, default 50 Hz, so command-to-motion granularity is 20 ms. **The autopilot cannot power servos**, so a release servo shares a BEC with whatever else is on that rail and a stalling control-surface servo can brown it out — see [test and verification](../test-and-verification.md) on separate power buses.

### Serial, routing and stream rates

**Connect over USB to the Pixhawk 6X rather than a UART.** `SERIALx_BAUD` ceases to matter, framing runs at full speed, and two of the five surveyed ArduPilot teams do exactly this. If a UART is required: `SERIALx_PROTOCOL = 2` and `SERIALx_BAUD = 921`, per ArduPilot's own companion tutorial. **Never 57600 for a companion** — that is a telemetry-radio rate, and position, attitude, RC and status at 10 Hz each already saturates it before mission upload.

**Put `mavlink-router` on the Jetson from day one, before anything else touches the port.** Serial-port ownership is a named blocker in Cornell's own risk table and is solved by every team who hit it. Prefer `/dev/serial/by-id/*` over raw device names so it survives enumeration order changes, and reserve spare UDP endpoints so adding a consumer needs no reconfiguration.

**Set the companion's stream rates with `MAVn_*` (not `SRn_*` on 4.7 and later), and set `MAVn_OPTIONS` bit 12, "Ignore Streamrate", on that link** so a ground station's `REQUEST_DATA_STREAM` cannot clobber our `SET_MESSAGE_INTERVAL` requests. **That bit moved out of `SERIALn_OPTIONS` in 4.7 and most advice online is stale.** Then do what the best-engineered surveyed team does: request a baseline all-streams rate **and** per-message intervals, and treat an interval rejection as a warning rather than a failure.

**Component IDs:** other MAVLink devices on the vehicle "should use the same System ID as the flight controller but use a different Component ID." Our companion must emit heartbeats before expecting bidirectional routing to work, because a ground station will not send commands to a sysid/compid combination until it has seen a packet from it.

### Integration bugs the field has already found

- **MAVROS's `home_position/req_update` service does not return.** Stream `HOME_POSITION` (message 242) instead.
- **ArduPilot resets home at arming**, so read it *after*.
- **`RC_CHANNELS_RAW` is only sent on MAVLink 1 links.** Use `RC_CHANNELS` (65).
- **Stream rates drop 4× during mission upload.**
- **`ROS_DOMAIN_ID` collisions** between teams on a shared field network.
- A GUIDED mode nobody asked for, because a geofence breach puts you there.

### Let the pilot own the mode

The cleanest safety architecture found in the survey is a behaviour-tree decorator whose docstring reads: "**The engine never changes flight mode itself: the pilot selects GUIDED to hand over control and any other mode to take it back.** While paused the child is not ticked, so nothing is commanded and its progress is kept; switching back to GUIDED resumes the mission where it left off."

Pair it with Cornell's invariants: a mode change from RC or the ground station **wins immediately**; stale telemetry older than 0.5 s stops commanding; the node **never commands outside AUTO or GUIDED**; and **ArduPilot's geofence stays authoritative.**

**And read before borrowing.** The best repositories in this field still ship code that has never run: one has a release hook commented out, a GUIDED hold commented out, and a file containing a guaranteed syntax error with a hardcoded sudo password. Another carries a "TODO: confirm against the actual drop hardware" on its payload constants.

---

## 9. Decisions and open items

| Decision | Choice |
| --- | --- |
| Critical command and telemetry | **900 MHz**, RFD900x class, with **orthogonal polarization diversity** on two ports |
| RC link | **ExpressLRS**, ESP-based hardware for MAVLink mode, 200 Hz on 900 MHz or 250 Hz on 2.4 GHz. **Never 25 Hz** |
| Video, if carried | **DJI O4** (no 2.4 GHz) with an aimed 9–10 dBic ground patch plus an omni on diversity. **Never 2.4 GHz video** |
| Consider instead | **RFD VideoLink plus TxMod**, putting everything on one 900 MHz band with one airborne antenna pair |
| Ruled out | Herelink, TBS Crossfire, Dragonlink, FrSky R9, LTE for C2 |
| Ground station | Moderate-gain patch on a **mast**, not a high-gain antenna on a tripod |
| Termination | `AFS_TERM_ACTION` — 43 (QLAND) preferred, 42 for literal rule compliance. **Ask the judges** |
| Companion interface | **MAVROS on ROS 2**, with pymavlink for what it gets wrong |
| Navigation architecture | **AUTO mission upload with spliced delivery waypoints**, not GUIDED streaming |
| Position commands | **`DO_REPOSITION`** or `MISSION_ITEM_INT` with `current = 2`. **Never `SET_POSITION_TARGET_GLOBAL_INT`** |
| Payload release | `DO_SET_SERVO` on two outputs, `SERVOn_TRIM` at grab, with a release-confirmation sensor |
| Companion connection | **USB**, with `mavlink-router` owning the port |

**Open items**

- **Ask the judges in writing** whether `AFS_TERM_ACTION = 43` (QLAND) satisfies the fixed-wing termination spec, or whether they require the literal surface deflections of 42.
- **Decide `Q_GUIDED_MODE` and write it on the airframe card**, in coordination with the payload release architecture.
- **Measure the carbon layup.** A two-antenna S21 on our own offcut at 915 MHz, 2.4 GHz and 5.8 GHz. Nobody has published it, and it would be an original contribution to the report.
- **Run a co-site acceptance test** against the 3GPP criterion, logging RSSI and noise before and after each configuration change at a fixed distance.
- **Range-test from altitude, not from the ground.** Ground-level tests produce non-monotonic RSSI from two-ray multipath and do not predict flight.
- **Write down the UT Austin range-collapse story** if anyone can source it, and ask Dr. Sirohi and Texas Aerial Robotics.
- Retrieve the Rutgers 2018 journal paper, the most promising remaining candidate for a documented lost-link narrative; its host refuses connections and appears down rather than blocking.
- No measured carrier-to-noise-versus-picture-quality curve exists for any digital video system, and no vendor publishes a minimum separation between an onboard video antenna and an RC antenna. **Both would have to be measured ourselves.**
