# Payload delivery

**Subsystem owner:** Payload lead (sub-team charter in the [program plan](../suas-program-plan.md)).

**Status:** Research complete, v1. Physics worked from first principles; the two parameters the whole estimate rests on are unmeasured and must be measured. Rules cited are the 2026 handbook. Re-verify against 2028.

Air delivery has been a scored SUAS task in **every cycle from 2014 to 2026** — see [rules watch](../rules-watch.md). It is one of the four capabilities worth building before the handbook publishes.

**Three requirements read directly from the 2026 handbook that constrain the mechanism** ([handbook 2026 §5.5](../handbook-2026.md#55-search-detect-and-deliver)):

1. **The 2 lb payload limit covers everything that separates from the aircraft**, the delivery object included. With a 255 g bottle that leaves about 650 g for canopy, carrier and release hardware, which is not tight.
2. **A judge must be able to free the object from our payload by hand, with no tools and no instructions, or the drop does not count.** That is a usability requirement on the carrier, not just a mechanical one: it has to be obvious to someone who has never seen it.
3. **If several payloads are released at once, only the best-scoring one counts.** There is no value in dropping both objects at one target.

Also: an attempted delivery is any payload released in flight, successful or not; and the delivery failsafe is explicitly named by the handbook as safety-critical functionality that must run on onsite systems with no internet or cloud dependency.

---

## 1. The scoring arithmetic, which reframes the problem

Per delivery, 100 points: payload survives 20, lands within 50 ft of the target 50, correct target 30. Two deliveries, 200 points total. Against that:

| Event | Points |
| --- | --- |
| Both deliveries perfect | **+200** |
| One delivery inside 50 ft | +50 |
| **One item departs the aircraft in flight** (10% of 1000) | **−100** |
| **One crash** (50% of 1000) | **−500** |

One unintentional departure costs exactly as much as one complete perfect delivery, and half of the entire task. A crash costs 2.5× everything the task can ever pay. And the crash risk is measured, not hypothetical: the 2026 results report **55 flights and 21 crashes** across 64 teams — 38% per flight.

**Therefore retention reliability outranks accuracy in expected value, and not breaking the aircraft outranks both.** Every trade below is resolved on that basis, and section 10 argues that this logic, pushed further, would produce a simpler subsystem than the one recommended here.

## 2. The rules, quoted

From handbook section 3.6:

- Objects are a **GP908 strobing beacon, approximately 155 g** with 3 AAA cells installed, delivered to a **tent**; and an **8 oz plastic water bottle, approximately 255 g**, delivered to a **mannequin**.
- "Each independent delivery payload must be no heavier than 2lbs."
- The payload "must not contain any ability to sustain flight (propulsion, propellers, etc.)."
- "**Payloads that are delivered in freefall, with no form of retardant mechanism, will not be deemed successful.**"
- "the UAS must remain above the 150' AGL minimum altitude fence while conducting deliveries."
- "UAS must carry all delivery objects they plan to drop at the same time - landing to re-load payloads is NOT permitted."

From Appendix B, the safety inspection:

- "All Fasteners Must Have Safety Wire, Loctite (Fluid), or Nylon Nuts."
- "**The delivery payload includes everything that separates from the UAS during a delivery, including the object being dropped.**"
- Safety-critical functionality, including the delivery failsafe, "cannot have a dependency on the public internet or public cloud providers."

From section 3.7, the penalty is for "parts fall off the UAS during flight" — with no qualifier about intent.

### What the no-sustained-flight clause actually forbids

The clause is about lift generation, not energy storage. Reading it against the handbook's own clarifier, "propulsion, propellers, etc.":

| Device | Verdict |
| --- | --- |
| Drag-only decelerator — round, cruciform, streamer, cone | **Permitted.** Generates drag, not lift |
| Ram-air or steerable gliding canopy | **Rejected.** It has a glide ratio; it sustains flight |
| Autorotating device — samara, rotachute | **Rejected.** It has a rotor generating lift, despite attractive descent rate per unit mass |
| The beacon's own 3 AAA cells | Not a risk. The organisers define the object as the beacon with batteries installed |

Because "the delivery payload includes everything that separates," **the canopy and its lines are part of the payload** and must stay attached to the object. A deployment bag that stays with the aircraft and departs later is arguably a part falling off, at 100 points.

---

## 3. The physics

3-DOF point mass, quadratic drag acting on the air-relative velocity vector, RK4 at 0.5 ms. A vacuum `v·t` throw calculation overstates horizontal throw by about 30% and is not usable.

Constants: g = 9.80665, rho = 1.18 kg/m³ (warm day near sea level; rho enters descent rate as rho^−1/2, so a 4% error is 2% in v_d), h = 45.7 m (the 150 ft floor, which is the binding case).

Reference areas from the Cauchy relation for a tumbling convex body, A_mean = S_surface/4, because neither object is aerodynamically stable in free fall:

- **Beacon**, 155 g, assumed 95 × 60 × 45 mm: A_mean = 0.00634 m², C_d about 0.9, so **C_dA = 0.0057 m²**, bounds 0.0024 to 0.0080. Terminal velocity 21.3 m/s. The dimensions are an assumption — the handbook gives mass only. Get the real article and re-run.
- **Bottle**, 255 g, 57 mm dia × 140 mm: A_mean = 0.00754 m², **C_dA = 0.0068 m²**, bounds 0.0023 to 0.0096. Terminal velocity 25.0 m/s.

### Bare ballistic drop from 150 ft

| Object | Release speed (m/s) | Fall time (s) | Throw (m) | Impact (m/s) | Energy (J) |
| --- | ---: | ---: | ---: | ---: | ---: |
| Beacon | 0 (hover) | 3.57 | 0.0 | 19.7 | 30.2 |
| Beacon | 15 | 3.71 | 32.4 | 19.8 | 30.2 |
| Beacon | 25 | 3.81 | 47.4 | 19.8 | 30.3 |
| Bottle | 0 (hover) | 3.43 | 0.0 | 21.8 | 60.6 |
| Bottle | 15 | 3.54 | 35.4 | 22.0 | 61.9 |
| Bottle | 25 | 3.64 | 53.0 | 22.2 | 63.0 |

**Release at the floor, not above it.** Impact energy saturates near terminal velocity, while fall time — and therefore wind drift — grows linearly with altitude. From 150 ft to 300 ft the beacon's impact energy rises only 30.2 to 34.4 J while fall time rises 3.57 to 5.79 s.

**And here is why a bare drop cannot be aimed.** Sweeping C_dA across its plausible range for a release at 22 m/s:

| Object | Throw at min / nominal / max C_dA (m) | Spread |
| --- | ---: | ---: |
| Beacon | 54.7 / 43.3 / 37.7 | **17.0 m** |
| Bottle | 59.4 / 48.1 / 43.0 | **16.4 m** |

A tumbling object's attitude history is not predictable, so a bare object released from cruise carries an irreducible **±8 m 1-sigma model error — over half the 15.24 m scoring radius, before any wind at all.** Attaching a decelerator fixes C_dA and collapses this term. That is an accuracy argument for a retardant device, independent of the rule and independent of survival.

### Sizing a decelerator

v_d = sqrt(2mg / rho·C_d0·S_0), so S_0 = 2mg / (rho·C_d0·v_d²) and D_0 = sqrt(4·S_0/pi).

Watch the two incompatible C_d conventions — getting this wrong undersizes a canopy by about 55%. The aerospace convention references C_d0 to the **nominal flat constructed area** S_0 (flat circular 0.75 to 0.80, cruciform 0.60 to 0.85); the rocketry convention references it to the **projected inflated area** (1.3 to 1.6). They reconcile: a commercial 120 in canopy quoted at C_d 1.5 projected and 76.4 in equivalent diameter gives D_p/D_0 = 0.637, so C_d0 = 1.5 × 0.637² = 0.61.

Sizing at C_d0 = 0.75, with the equivalent free-fall drop height that each impact speed corresponds to:

| v_d (m/s) | ft/s | Beacon D_0 (in) | Bottle D_0 (in) | Fall time (s) | Bottle energy (J) | Equivalent drop |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 3 | 9.8 | 27.4 | 35.2 | 15.2 | 1.1 | 0.5 m |
| 5 | 16.4 | 16.5 | 21.1 | 9.1 | 3.2 | 1.3 m |
| 8 | 26.2 | 10.3 | 13.2 | 5.7 | 8.2 | 3.3 m |
| 10 | 32.8 | 8.2 | 10.6 | 4.6 | 12.8 | 5.1 m |
| **12** | **39.4** | **6.9** | **8.8** | **3.8** | **18.4** | **7.3 m** |
| 15 | 49.2 | 5.5 | 7.0 | 3.0 | 28.7 | 11.5 m |
| 20 | 65.6 | 4.1 | 5.3 | 2.3 | 51.0 | 20.4 m |

That last column is the unlock for the test program: **a 12 m/s impact is a 7.3 m (24 ft) drop onto the same surface** — a parking garage, a stairwell, a third-floor window. You can do that a hundred times for free. See section 7.

**The same table kills the streamer option.** Expressed as required drag area, reaching 10 m/s on the bottle needs 6.2× the bare body's drag area, about 350 cm² of equivalent flat plate; at 20 m/s it needs only 1.6×, which is to say a streamer is nearly indistinguishable from freefall. A flexible ribbon cannot deliver 6× its own planform. Streamers are rejected on arithmetic.

### Wind drift, which is the crux

Drift = V_wind × fall time = V_wind × h / v_d. At h = 45.7 m, bold exceeds the 15.24 m scoring radius on wind alone:

| v_d (m/s) | 5 kt | 10 kt | 15 kt | 20 kt |
| ---: | ---: | ---: | ---: | ---: |
| 3 | **39.2** | **78.4** | **117.6** | **156.7** |
| 5 | **23.5** | **47.0** | **70.5** | **94.0** |
| 8 | 14.7 | **29.4** | **44.1** | **58.8** |
| 12 | 9.8 | **19.6** | **29.4** | **39.2** |
| 15 | 7.8 | **15.7** | **23.5** | **31.3** |
| 20 | 5.9 | 11.8 | **17.6** | **23.5** |

At 4 m/s in 15 kt the drift is 88 m — 5.8× the scoring radius. **This is why the slow-and-gentle instinct is wrong.**

But drift is not error, because you can aim off. Error is driven by wind **uncertainty**, delta-W × fall time — plus a term that no descent rate can touch. A canopy that glides or oscillates at a fraction k of its descent rate drifts k·v_d·(h/v_d) = **k·h, independent of descent rate**: 4.6 m at k = 0.10, 11.4 m at k = 0.25. Published drop-test data showing C_d 2.40 gliding against 1.26 restrained is direct evidence that small round canopies glide substantially. **Canopy choice is the only lever on this term.**

How big is delta-W really? METAR practice reports a gust when the peak exceeds the two-minute mean by 9 to 10 kt (4.6 to 5.1 m/s). So even a perfect mean-wind estimate leaves several m/s of unsteadiness across the 3 to 5 s the payload is airborne. **delta-W = 1 m/s needs a measured wind and a calm day; delta-W = 2 to 3 m/s is the honest planning number.**

### Finding the optimum, rather than assuming slower is better

sigma²_total(v_d) = sigma²_release + (delta-W·h/v_d)² + (k·h)² + (c·v_d)²

where the last term is bounce and roll on landing. Only the second and fourth depend on v_d, so setting the derivative to zero gives

**v_d,opt = sqrt(delta-W · h / c)**

At h = 45.7 m, delta-W = 2 m/s and c = 0.10 s, that is **30 m/s** — essentially freefall. The full curve, with hit probability as a Rayleigh circular-normal P(R < R_0) = 1 − exp(−R_0²/sigma²_rms):

| v_d | wind term | glide | bounce | sigma_total | **P(within 50 ft)** |
| ---: | ---: | ---: | ---: | ---: | ---: |
| 3 | 30.5 | 6.9 | 0.3 | 31.5 | 20.9% |
| 5 | 18.3 | 6.9 | 0.5 | 19.9 | 44.3% |
| 8 | 11.4 | 6.9 | 0.8 | 13.9 | 69.8% |
| 12 | 7.6 | 6.9 | 1.2 | 11.1 | **85.0%** |
| 15 | 6.1 | 6.9 | 1.5 | 10.1 | 89.7% |
| 20 | 4.6 | 6.9 | 2.0 | 9.4 | 92.9% |
| 30 | 3.0 | 6.9 | 3.0 | 9.0 | 94.3% |

**The aerodynamic optimum is at essentially freefall, and the curve is nearly flat above 15 m/s.** The real optimum is therefore a constrained one: the fastest descent the payload survives and the judges accept. Anyone who reports an interior optimum at 5 to 6 m/s has left the wind term out of their model.

Diminishing returns: 5 to 12 m/s buys **+40.7 points of hit probability**; 12 to 20 buys +7.9; 20 to 30 buys +1.4. **The knee is at 12 to 15 m/s.**

### A deliberate opening delay is strictly better

Freefall for a set delay, then let the canopy fill. Bottle, 10 m/s canopy, 10 kt crosswind:

| Delay (s) | Fall time (s) | Drift (m) | Impact (m/s) |
| ---: | ---: | ---: | ---: |
| 0.2 | 5.30 | 19.1 | 11.2 |
| 1.0 | 5.05 | 15.9 | 11.2 |
| 1.5 | 4.75 | 13.0 | 11.2 |
| 2.0 | 4.33 | **9.9** | 11.2 |

A 2 s delay cuts 10 kt drift by 48% while impact speed rises only 10.0 to 11.2 m/s, because the object overshoots v_d and the canopy brakes it back. It also guarantees dynamic pressure at inflation, which a hover release otherwise lacks entirely. **Use 1.0 to 1.5 s**, which still leaves 33 to 35 m of inflated descent from the 150 ft floor.

### What the objects survive

Military airdrop practice is the strongest available anchor. Low-velocity aerial delivery descends at **28.5 ft/s (8.7 m/s) or less**; **high-velocity at 50 to 90 ft/s (15 to 27 m/s)** for robust supplies. A sealed plastic water bottle is a robust supply, and **255 g at 12 m/s (39 ft/s) sits below the high-velocity band.**

Model-rocketry targets of 3.5 to 4.5 m/s and NASA student-launch's 18 ft/s protect thin fibreglass tubes and avionics bays. Do not import those numbers.

The bottle will survive 12 to 15 m/s onto grass. **The beacon is the binding constraint** — a plastic housing with AAA cells and a strobe circuit, whose failure mode is a cracked lens or dislodged battery, which also forfeits the 20-point survival score. Protect it with 6 to 10 mm of closed-cell foam at about 5 g, rather than with a slower canopy: halving v_d costs 4× canopy area and doubles drift.

---

## 4. Release mechanism

### The decisive criterion is not holding force

155 g and 255 g are trivial loads for anything on the list. The criterion is: **does the mechanism hold the payload when the electronics are unpowered, booting, browning out, glitching, or in failsafe?** That splits the field into fail-safe retained and actively held.

### Two documented ArduPilot traps that cost 100 points each

**`SERVOn_FUNCTION = 0` leaves the output unassigned, and an unassigned output emits `SERVOn_TRIM` as soon as board safety is cleared.** If trim corresponds to the release position, the mechanism releases on the ground, before takeoff — and the rules forbid reloading. **Set `SERVOn_TRIM` to the grab position.** That single parameter is worth 100 points.

**`FS_OPTIONS` contains a bit that releases the gripper on failsafe.** Read that twice: there is a configuration in which a radio failsafe intentionally drops your payload. Verify it is disabled.

Related facts: AUX outputs run at `SERVO_RATE`, default 50 Hz, so command-to-motion granularity is 20 ms; the autopilot cannot power servos, so a release servo shares a BEC with whatever else is on that rail and a stalling aileron servo can brown it out; and the gripper driver supports only **one** gripper, so two payloads cannot both use it.

### The comparison

| Mechanism | Fail-safe retained? | Verdict |
| --- | --- | --- |
| Servo hook or claw, held by torque | **No** — back-drivable, and moves to trim at boot | Simplest, highest departure risk |
| **Servo-tripped over-centre sear latch** | **Yes** — load path goes through a mechanical stop; servo only trips the sear and idles at 8 mA | **Recommended** |
| Plain electromagnet | **No, catastrophically** — drops on any power loss, and draws 0.3 to 1 A continuously | Reject |
| EPM (electro-permanent magnet) | Yes — zero holding power | The widely-cited OpenGrab EPM v3 is **discontinued and its vendor domain now resolves to an unrelated gambling site.** The current option publishes no holding force, mass or current on its public pages. Needs a datasheet before selection |
| Nichrome burn wire | Yes | Single-use per flight, slow, and awkward at the inspection table |
| Solenoid | Only if latching | Heavier than a servo for no gain |
| Pneumatic or CO2 | Yes, but a pressure vessel | Unjustified at 255 g |
| Winch or rotating drum | n/a | Means a long hover and a tether. One commercial unit publishes no payload, mass, voltage or price |
| Commercial DJI-platform droppers | Not stated | No rated load or retention claims published. Not selectable on evidence |

### The architecture that follows

Two physically separate, mechanically latched stations, one per payload. Separate BEC for the release servos, not the rail shared with control surfaces. Both servos' `SERVOn_TRIM` at the grab position. A **removable mechanical safety pin** in each latch, pulled at the flight line — which also gives the inspector something satisfying to look at. Every fastener in the load path gets safety wire, Loctite or a nylon nut, per Appendix B.

`MAV_CMD_DO_SET_SERVO` is what every surveyed team actually ships, over the gripper abstraction. But note the gap: **it acknowledges the command, not the drop.** Add a microswitch or photointerrupter, or at minimum log `SERVO_OUTPUT_RAW`. One surveyed team's winch reports a six-state status and is gated Complete before RTL — that is the right instinct.

---

## 5. Aiming

P_release = T_target − F(V_rel, C_dA, h) − W_eff × t_fall

The military analogue is a Computed Air Release Point using a mean effective ballistic wind. For a 45.7 m fall that collapses to a single surface-layer wind, which is convenient: **the entire relevant wind profile can be measured with one anemometer on a 2 m pole.**

### Wind estimation is the dominant error term, and the autopilot will not give it to you

ArduPilot estimates wind inside the EKF, tuned by `EKF_WIND_PNOISE` and `EKF_WIND_PSCALE`, with `EKF_EAS_NOISE` suggested at about 1.4 m/s including gusts. The documentation is explicit that airspeed innovation is higher early in a flight before wind is estimated, and that a steady airspeed offset — from calibration drift, a covered sensor at init, or temperature change — **biases the wind estimate**.

**It publishes no accuracy figure, and no validation against a sonic anemometer or met tower could be found.** The 1.4 m/s is an input assumption, not a measured output error.

**So measure the wind on the ground.** A $150 anemometer on a 2 m mast at the drop site, logged and telemetered to the GCS, directly attacks the largest term in the budget. **It is the highest-value, lowest-cost item in the entire subsystem** — see the sensitivity table in section 6.

### There is no off-the-shelf release-point solver

ArduPilot's own example deployment script triggers on a barometric altitude threshold, has no trajectory, drop-point or wind model at all, force-arms the vehicle bypassing pre-arm checks, and has its actual release line commented out. **You will write the solver.** One surveyed team has a complete worked parachute wind-offset model computed in UTM space — start from that rather than from scratch.

### Hover versus moving release

| Error source | Hover | Forward at 22 m/s |
| --- | ---: | ---: |
| Forward throw | **0** | 30.6 m, must be modelled |
| Ballistic C_dA model error | **0** | about 3 m with canopy, about 8 m bare |
| **Release latency, 200 ms** | about 0.1 m | **4.4 m** |
| Groundspeed scatter, ±1 m/s | 0 | 1.0 m with canopy |
| Cross-track tracking error | about 0 | about 3 m |
| Hover residual velocity | 1.3 to 2.1 m | 0 |
| Canopy fouling risk | **Higher** — rotor downwash | Lower |
| Mission time | 20 to 40 s per delivery | about 0 |

Hover eliminates four terms totalling 5.6 to 9.9 m per axis and adds one worth 1.3 to 2.1 m. In hit probability: **58.1% hovering against 50.0% at 22 m/s** with the same canopy, or 46.8% bare.

**But the time penalty is real.** Excess time costs 0.5% of demonstration points per second, so every 2 seconds costs 10 points. Two 30-second decelerate-hover-drop-accelerate cycles is 60 s — 300 points if it pushes you over the clock.

**Recommendation: a slow translating release at 5 to 8 m/s.** It keeps most of the hover benefit — latency drops to 0.5 to 0.8 m per 100 ms, throw to about 8 m and highly repeatable — while translating the airframe away from the payload, which matters because a 2 g canopy released into 6 to 10 m/s of rotor downwash is not ballistic, it is a leaf. Release must be below and outboard of the wake boundary, or in slow forward flight.

### Deployment reliability, and why the canopy should not be packed

At a true zero-speed hover release, dynamic pressure is zero, so a bagged canopy has nothing to inflate it. With a 200 mm canopy the filling distance is only 1.6 to 1.8 m, so in practice it streams, snatches and fills within the first 5 to 10 m. The failure mode is not "too slow", it is **"did not open at all"**: line tangle, bag lock, squidding, inversion, failure to inflate at low dynamic pressure, and the canopy fouling the aircraft on release.

With a canopy of 1 to 2 g packing into under 3 cm³ — about a thimble — **there is no reason to bag it.** Carry it **packed but unbagged**, folded into a shallow cup on the payload and retained by the latch geometry itself, so that release both frees the payload and opens the cup. No bag means no bag lock.

This matters because the obvious alternative does not survive contact with the airframe: an **always-open** rigid-mouth decelerator on the bottle adds roughly 0.039 m² of drag area, which at 22 m/s is **11.1 N on an aircraft whose total cruise drag is perhaps 15 to 25 N — a 45 to 75% increase.** Endurance is a 250-point task in its own right. Unacceptable, and the reason the always-open option was discarded mid-analysis.

---

## 6. Error budget and expected performance

Per-axis 1-sigma in quadrature; radial RMS = sqrt(2)·sigma_axis; CEP = 1.1774·sigma_axis; R95 = 2.4477·sigma_axis.

| Config | Decelerator | Release | delta-W | sigma_radial | CEP | **P(within 50 ft)** |
| --- | --- | --- | ---: | ---: | ---: | ---: |
| A | none (rule-illegal) | fwd 22 m/s | 2.0 | 19.18 | 15.97 | 46.8% |
| **B** | **5 m/s round** | **hover** | 2.0 | 33.50 | 27.89 | **18.7%** |
| C | 12 m/s cruciform | hover | 2.0 | 16.34 | 13.60 | 58.1% |
| D | 12 m/s cruciform | fwd 22 m/s | 2.0 | 18.30 | 15.23 | 50.0% |
| **E** | **12 m/s cruciform** | **hover, measured wind** | **1.0** | **10.15** | **8.45** | **89.5%** |
| F1 | 12 m/s cruciform | hover, release at 150 ft | 1.5 | 12.76 | 10.63 | 76.0% |
| F2 | 12 m/s cruciform | hover, release at 250 ft | 1.5 | 18.52 | 15.42 | 49.2% |

Three results worth reading twice:

1. **Config B — the gentle 5 m/s parachute — is the worst option on the board at 18.7%, worse than an illegal bare freefall.** The instinct to protect the payload with a slow canopy loses the 50-point accuracy award almost every time.
2. **Releasing at 250 ft instead of 150 ft halves hit probability**, 76.0% to 49.2%.
3. **Wind knowledge is worth more than any hardware.** C to E is 58.1% to 89.5%.

### Sensitivity, on config E

| Term | Base (m) | If halved | If doubled |
| --- | ---: | ---: | ---: |
| Target geolocation | 2.00 | 90.9% | 84.0% |
| **Wind estimate** | **4.20** | **95.2%** | **67.1%** |
| Nav position | 0.30 | 89.6% | 89.4% |
| Gust unsteadiness | 2.52 | 91.7% | 80.7% |
| **Canopy glide and oscillation** | **4.57** | **96.1%** | **63.9%** |
| Hover residual velocity | 1.26 | 90.1% | 87.3% |
| Bounce and roll | 0.96 | 89.8% | 88.2% |

**Only two terms matter: wind estimate error and canopy glide.** Each is worth about 6 points of hit probability if halved and 25 if doubled. **Nav position error is irrelevant — RTK buys 0.2 points.** So do not spend money on RTK; spend it on an anemometer and on a canopy you have actually characterised. That is the opposite of where most teams put their effort.

---

## 7. Test program

### Legal basis

14 CFR 107.23 says no person may "**Allow an object to be dropped from a small unmanned aircraft in a manner that creates an undue hazard to persons or property.**" Note what it does not say: dropping objects from a small UAS is **not prohibited**, only dropping them hazardously. The campaign needs a cleared area, no non-participants, and a documented hazard analysis. Confirm the rest against [FAA compliance](../faa-compliance.md).

### The tiers

| Tier | Rig | Cost | Drops/hour | What it characterises |
| --- | --- | --- | ---: | --- |
| 0 | Hand drop, 2 m | $0 | 100+ | Latch cycling, canopy rigging |
| **1** | **Parking garage or stairwell, 7 to 8 m** | **$0** | **40 to 60** | **Impact survivability at the design descent rate.** Instrument a dummy beacon with a $15 accelerometer logger |
| **2** | **Building or drop tower, 15 to 25 m** | **$0** | **20 to 30** | **Canopy inflation from zero airspeed, steady v_d filmed against a scale, oscillation amplitude — the k term** |
| **3** | **Hovering multirotor at 45.7 m** | owned | 10 to 15 | **Full-system v_d and k, plus the wake-fouling question** |
| 4 | Full quadplane, mission profile | — | 4 to 6 | Release latency, nav error, integrated sigma |
| 5 | Weather balloon | $50 | 1 | Not worth it — you need repetitions, not altitude |

**Spend the time on tiers 2 and 3**, because they measure the two terms the sensitivity table says matter. Tier 4 is expensive per data point and mostly measures the autopilot, which can be characterised without dropping anything by flying to a surveyed point and logging position error.

### How many drops

Reliability, by the rule of three — zero failures in n trials bounds the failure rate at 3/n with 95% confidence:

| To show failure rate below | Drops needed |
| --- | ---: |
| 20% | 15 |
| **10%** | **30** |
| 5% | 60 |

Confidence on the hit rate itself, Wilson 95%:

| Observed | 95% interval on P(hit) |
| --- | --- |
| 9/10 | **[60%, 98%]** |
| 18/20 | [70%, 97%] |
| 27/30 | [74%, 97%] |
| 45/50 | [79%, 96%] |

**Read the first row honestly: 9 hits out of 10 tells you almost nothing — your true rate could be 60%.** Claiming a reliable 50 ft radius needs **n of about 30 full-system drops minimum**, and that is still only "better than 74%". This is the number to put in the test plan, and the number most teams will not have.

### Measuring miss distance

A **surveyed ground grid** — stakes or chalk circles at 5/10/15/20 m, pre-measured — gives sigma about 0.2 m, costs an afternoon, works forever and shows the judges' eye view. Use overhead imagery with a known ground sample distance for throughput, referenced against that grid in the same frame. A bare consumer GPS logger on the payload has 1.5 to 2.5 m CEP, the same order as the thing being measured, so it is useless without RTK — and an RTK module is a large fraction of the beacon's mass budget.

Log release position, velocity and attitude, the autopilot's wind estimate, the anemometer trace and the measured landing point, every drop. **With 30 of those you can regress your actual delta-W and k instead of using assumed values. That is the real deliverable of the test program.**

### The calibration-drop result, which is counterintuitive

You carry both payloads from the start, so drop 1 is free information: observe the error, apply it as a bias correction to drop 2. Decomposing each error into a shared bias b and a random part r, and correcting with an observation of noise sigma_obs:

e2_corrected = r2 − r1 − n_obs, so sigma²_corrected = 2·sigma²_r + sigma²_obs against sigma²_uncorrected = sigma²_b + sigma²_r

**Correction helps if and only if sigma²_b > sigma²_r + sigma²_obs.**

| sigma_b | sigma_r | sigma_obs | Uncorrected | Corrected | Helps? |
| ---: | ---: | ---: | ---: | ---: | --- |
| 12.0 | 5.0 | 1.0 | 49.7% | 89.7% | **Yes, hugely** |
| 9.0 | 7.2 | 1.5 | 58.3% | 66.6% | Yes |
| **4.7** | **5.5** | **1.5** | **89.1%** | **84.3%** | **No** |

Monte Carlo over 200,000 trials on config E confirms it: expected delivery points 89.5/100 uncorrected against **87.1/100 corrected**. **The obviously smart iterative correction is actively harmful in the well-tuned configuration and strongly helpful in the badly-tuned one.** So it is a live calculation, not a design choice — correct if you do not have a measured wind, do not correct if you do. Put the test in the flight software, not in the plan.

---

## 8. Materials and environmental gotchas

**No adhesive in any load path.** EVA hot-melt provides sufficient strength only between 30 and 50 °C, is limited to below 60 to 80 °C, and — the decisive property — has **low creep resistance under load**. A hot-glue joint holding a hanging payload at 39 °C is at the bottom of its band and creeping. The widely-repeated account of a competition team's release servo failing when its hot glue melted on a hot day needs no anecdote to be credible; it is in the published adhesive properties. Adhesive is for wire strain relief, nothing else.

**No PLA in any load path.** It softens above 60 °C, is explicitly "not suitable for technical and outdoor use", and degrades under UV. A dark part on a flight line in direct sun plausibly exceeds 60 °C. Use PETG, ASA, PC or nylon, and keep sustained load off printed plastic entirely: a printed sear that *trips* is fine; a printed hook that *hangs* for a 40-minute mission creeps the whole time.

**No rubber or latex in the retention or deployment path.** Natural rubber is sensitive to ozone cracking and is damaged by ageing, sunlight and oil. This is why rocketry replaced rubber shock cords with Kevlar and Nomex.

**New nylocs each competition.** Nyloc retains locking to 121 °C per NASM 25027; FAA AC 43.13-1B permits reuse while prevailing torque stays in spec, but military practice requires replacement in critical areas. Treat the release latch as critical.

**A servo holding against a load draws current and heats all mission** — one surveyed micro servo draws 8 mA idle against 120 mA running. Another argument for the over-centre latch, in which the servo is unloaded and idle for the whole flight.

**Log a pre- and post-flight torque check on every load-path fastener** and put the record in front of the inspector. Free points in the safety assessment, and it catches the failure before it costs 100.

**CG shift is a non-issue if the stations straddle the CG.** Releasing both payloads plus decelerators (460 g from 6.8 kg) moves the CG 7.3 mm — 2.9% of a 250 mm mean chord — at a 10 cm arm, rising to 14.5 mm and 5.8% at 20 cm. Mount both stations within about 10 cm of the CG longitudinally and release from opposite sides laterally. The in-flight *shifting* risk from a payload swinging in a soft restraint matters more than the release step, which is another argument for a rigid latch.

---

## 9. Recommendation

| Decision | Recommendation |
| --- | --- |
| **Retardant device** | **Cruciform (X-type) canopy at C_d0 about 0.70, sized for v_d = 12 m/s.** Beacon 0.0256 m² (D_0 180 mm, arm about 225 mm); bottle 0.0420 m² (D_0 231 mm, arm about 287 mm). Lines at 1.0 to 1.5 D_0. 0.66 to 1.1 oz ripstop, 1 to 2 g, under 3 cm³ packed |
| **Deployment** | **Packed but unbagged**, in a shallow cup opened by the release itself, with a deliberate **1.0 to 1.5 s freefall delay** |
| **Release** | **Two independent servo-tripped over-centre sear latches**, dedicated BEC, `SERVOn_TRIM` at the grab position, removable ground safety pin in each |
| **Impact protection** | 6 to 10 mm closed-cell foam on the **beacon only** |
| **Aiming** | Open-loop ballistic solver, **slow translating release at 5 to 8 m/s**, at **exactly the 150 ft floor**, with the wind **measured by a ground anemometer** at the drop site |
| **Two-pass correction** | **Conditional, computed in flight**: apply only if sigma²_b > sigma²_r + sigma²_obs |
| **Reject** | Ram-air and steerable canopies; autorotating devices; streamers; plain electromagnets; canopies at 6 m/s or slower; any release held only by servo torque; RTK as an accuracy investment |

**Expected performance: CEP about 8.5 m (28 ft), radial RMS about 10 m, R95 about 17.6 m, P(within 50 ft) about 89%** — assuming a measured ground wind and a characterised canopy.

**The honest uncertainty on that number.** Without a measured wind and a characterised canopy it falls to about **58%**. The two driving parameters, delta-W and k, are both **assumed**: delta-W = 1.0 m/s is achievable but unproven, and k = 0.10 for a cruciform is a literature-flavoured guess. If k is really 0.20, hit probability drops to about 64% and **no amount of wind measurement recovers it**. So the stated 89% carries roughly a −25 / +8 point range until 30 instrumented drops exist. Expected delivery score: **175/200 optimistic, 115/200 pessimistic.**

### First three things to build

1. **The over-centre sear latch, in metal, with the safety pin** — then cycle it 500 times on the bench and 50 times under a 1 kg shock load. This protects the biggest number in the scoring table.
2. **A tier-2 drop rig** — stairwell or garage, surveyed grid, accelerometer logger in a dummy beacon — and 30 drops of each candidate canopy, filmed against a scale, to measure v_d and k.
3. **The ground anemometer and its telemetry path into the release-point solver.** Cheapest point per dollar in the subsystem.

---

## 10. The case against this recommendation

**The 12 m/s descent rate may fail the judge, not the physics.** The rule requires a "retardant mechanism" and does not define one. A judge watching a 255 g bottle arrive at 39 ft/s under a 9-inch canopy may reasonably call it freefall with a handkerchief. The rule's purpose is visible, demonstrable retardation. The downside is not 5 m of accuracy — it is the entire 100-point delivery scored unsuccessful. **The defensive alternative is 8 m/s (26 ft/s), inside low-velocity airdrop practice and visually unambiguous, at about 70% hit probability.** Expected points: 170 against 189 if the judge accepts 12 m/s, but 170 against **100** if they do not. **With a single unrecoverable mission on the line, take the 8 m/s canopy.** Resolve it by asking the organisers in writing well before the event, and bring both.

**The glide term may be the whole ballgame.** It is co-dominant, independent of descent rate so no v_d choice escapes it, and **every number for it here is assumed.** If small cruciforms at Reynolds about 10^4 oscillate far worse than large ones — physically plausible, since inflation stability degrades at low Reynolds number — then a **rigid drag cone**, which cannot oscillate because it has no flexible canopy, becomes the right answer despite its lower C_d and bulkier carriage. **Test the cone against the cruciform in tier 2.**

**The hover architecture may lose on mission time.** The 5 to 9 m it buys was traded against the 0.5%-per-second penalty without the actual lap-time budget in hand. If the budget has no slack, two decelerations cost 300 points and the correct answer is a 22 m/s forward release at 50% hit probability, or skipping one delivery. **Get the time budget before committing.**

**The carriage-drag fix is a sketch, not a design.** The always-open decelerator was rejected on an estimated 45 to 75% cruise drag increase, with no measured external-store drag data for a small UAV found. The packed-but-unbagged-in-a-cup resolution reintroduces some inflation risk. If the fairing does not work out, you are back to bagged canopies with a static line or pilot chute, and section 5 needs redoing.

**And the largest objection: this optimises accuracy when the data says to optimise for not crashing.** 21 crashes in 55 flights. A crash is −500; the whole task is +200. A dispassionate expected-value analysis might conclude the optimal subsystem is **the simplest, lightest, lowest-risk one that reliably scores survival plus correct target — 50 points per object — and treats the 50-point accuracy award as a bonus**: no hover, no added drag, minimal mechanism, both payloads on one high-speed pass. That scores 100 to 160/200 and materially reduces the 38% crash probability. **It cannot be ruled out that this beats the recommendation above on expected total score, and if the airframe is not yet flying reliably, it almost certainly does.**

---

## 11. Open items

- **Get the real GP908 article** and re-run section 3 with measured dimensions rather than the assumed 95 × 60 × 45 mm box.
- **Ask the organisers in writing** what qualifies as a retardant mechanism, and whether a descent rate has a floor.
- Pull the Knacke *Parachute Recovery Systems Design Manual* (NWC TP 6575) for primary C_d0 and allowable-impact-velocity tables — DTIC was under maintenance during this research, so every Knacke-attributed number here is from a secondary reproduction.
- Pull FAA AC 43.13-1B for safety-wire twist rate and group size, needed for both the report and the inspection.
- Get the Loctite 243 technical data sheet before specifying a threadlocker grade.
- **Mine the RoboNation team journal paper archive.** Public Google Drive folders hold team papers by year back to 2004 — dozens of teams times fifteen years of air-delivery subsystem write-ups, and nobody appears to analyse them. The 2026 per-team score spreadsheet would also show who actually scored delivery points. This is the cheapest remaining research and the most likely to overturn something above.
