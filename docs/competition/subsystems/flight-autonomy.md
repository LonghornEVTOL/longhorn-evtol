# Flight autonomy and navigation

**Subsystem owner:** Flight software lead.

**Status:** Partial, v1. Section 1 answers the waypoint-detection question from the ArduPilot source and is complete. The wider QuadPlane parameter set is the largest remaining gap in the program — see section 4.

Autonomous waypoint navigation has been scored in **every SUAS cycle from 2014 to 2026** — 12 of 12, the longest-surviving requirement in the competition. See [rules watch](../rules-watch.md).

---

## 1. How a waypoint is actually detected

This matters more than it looks, because **ArduPilot's idea of "reached" and the judges' idea of "within 100 ft" are two different measurements, and they can disagree by tens of metres.**

### What the autopilot does

`Plane::verify_nav_wp()` in `ArduPlane/commands_logic.cpp` returns true — marking the waypoint complete and sequencing to the next — when **either** of two conditions holds:

**Condition A, by distance.** If the distance from the current position to the target is within an acceptance distance, it emits "**Reached waypoint**" and completes.

**Condition B, by line crossing.** If `current_loc.past_interval_finish_line(prev_WP_loc, flex_next_WP_loc)` is true, it emits "**Passed waypoint**" and completes. This is a finish line drawn through the waypoint perpendicular to the leg from the previous waypoint. **It exists so the aircraft does not circle forever after missing a waypoint by more than the radius** — and it will fire regardless of how far away the aircraft was when it crossed.

### The acceptance distance is not `WP_RADIUS`

This is the detail that catches people. The acceptance distance is:

```
nav_controller->turn_distance(get_wp_radius(), auto_state.next_turn_angle)
```

**It is a function of `WP_RADIUS` and the turn angle at that waypoint.** A sharper turn yields a larger acceptance distance, because the controller must begin the turn early to fly a physically achievable arc. So **the aircraft legitimately cuts the corner by an amount that grows with the turn angle**, and the autopilot marks the waypoint complete while the aircraft is still some distance from it.

Two overrides exist, both carried in the mission command's `p1` field:

| `p1` | Effect |
| --- | --- |
| Low byte nonzero | That value in **metres** is used as the acceptance distance directly, bypassing the turn-angle calculation |
| High byte nonzero | A **pass-by** offset: the target is pushed that many metres further along the bearing from the previous waypoint, and acceptance distance becomes **0 — the waypoint counts as reached only when overflown** |

And a guard: **`WP_MAX_RADIUS`**, when greater than zero, makes the function return false early if the distance to the waypoint exceeds it — preventing a waypoint from being marked reached from far away.

### What this means against the SUAS rules

The rules require waypoints hit **within 100 ft (30.5 m), in order**, up to 15 waypoints per lap, and the safety inspection requires the GCS to display a "**Waypoint Threshold Configured to Less Than 50 Feet**" — so `WP_RADIUS` must be under **15.24 m**.

**The trap:** if the judges measure our closest approach from the flight log while ArduPilot measures its own sequencing, then a waypoint the autopilot cheerfully reports as "Passed" by line crossing may have been missed by 60 m. **The autopilot's console message is not evidence of scoring compliance.**

**Three consequences for how we fly and verify:**

1. **Set `WP_RADIUS` small — under 15.24 m — and set `WP_MAX_RADIUS` as a hard guard**, so a waypoint cannot be marked complete from outside the scoring radius.
2. **Do not rely on tightening `WP_RADIUS` to fly through tight geometry.** The documented field experience is that a too-small radius causes *overshoot*, because the controller still flies the crosstrack and the turn still has a physical radius. One operator shrank it to fly through gates and still missed by 10–15 m in simulation. **The fixes that work are lead-in and lead-out waypoints and `NAVL1_*` tuning, not a smaller radius.**
3. **Verify by post-processing the log for closest approach to each waypoint**, not by counting "Reached waypoint" messages. That check belongs in the automated log review in [test and verification](../test-and-verification.md), and it belongs in the technical design report as measured data — predicted versus achieved waypoint error is exactly the comparison the [documentation rubric](../documentation-strategy.md) rewards most.

### The turn-radius constraint this sits inside

The qualification video requires a **turn radius of 150 ft (45.7 m) or less**. At our 22–25 m/s cruise that is demanding: turn radius is `v² / (g · tan φ)`, so 45.7 m at 22 m/s needs a bank angle of about **46°**, and at 25 m/s about **54°**. ArduPilot's reliability guidance separately notes that at 30 m/s with a 30° bank the turn radius **exceeds 200 m**, and that wind widens it significantly.

**So the 150 ft turn radius and the 100 ft waypoint tolerance are coupled constraints**, and they are the reason `NAVL1_PERIOD` and `LIM_ROLL_CD` are on the critical path rather than being incidental tuning. One surveyed competitor flies a quadplane at 25 m/s cruise with `LIM_ROLL_CD 6500` — a 65° bank limit — and `NAVL1_PERIOD 14`.

---

## 2. What is already documented elsewhere

Rather than duplicate, these are covered in full in neighbouring documents:

| Topic | Where |
| --- | --- |
| `Q_GUIDED_MODE` and what GUIDED does on a quadplane | [avionics and datalink](avionics-and-datalink.md) section 8 |
| Why `SET_POSITION_TARGET_GLOBAL_INT` silently fails on Plane, and what to use instead | same, section 8 |
| AUTO mission upload versus GUIDED setpoint streaming, and the decision | same, section 8 |
| Lost-link failsafe, the 15-second rule, and the flight-termination spec | same, sections 1 and 4 |
| The 13-step VTOL tuning sequence with ArduPilot's own numbers | [test and verification](../test-and-verification.md) section 6 |
| SITL, failure injection, and the CI regression strategy | same, sections 2 and 3 |
| The ten flight-test gates from bench to autonomous mission | same, section 6 |

---

## 3. What we know about the QuadPlane parameter set

Partial, and flagged as such. Verified items:

- **`Q_ASSIST_SPEED`** should sit about 3 m/s below `AIRSPEED_MIN` but above stall, so assist does not engage within normal operating speeds. `AIRSPEED_MIN` itself should be 25% above stall.
- **`Q_TRANS_FAIL`** and **`Q_TRANS_FAIL_ACT`** should be set, with QRTL as the action unless flying beyond visual line of sight.
- **`TECS_LAND_ARSPD`** just above `AIRSPEED_MIN`, or the approach uses cruise airspeed and enlarges the turns.
- **`Q_OPTIONS` bit 5** makes an RC failsafe in VTOL modes QRTL rather than QLAND.
- **`BATT_FS_LOW_ACT = 6`** gives a fixed-wing return, a loiter down to `Q_RTL_ALT`, then QLAND.
- **Landing legs must be long enough to correct turn overshoot before the VTOL transition**, given the turn radii above.
- ArduPilot's own instruction: **test the landing sequence in SITL using the `-f quadplane` frame with our TECS settings and pitch and roll limits.**
- The SITL quadplane model's defaults are **not our aircraft** — `AIRSPEED_MIN 13`, `AIRSPEED_MAX 35`, `AIRSPEED_CRUISE 25`, `Q_ASSIST_SPEED 18`, a 3S battery model, `ROLL_LIMIT_DEG 65`.

---

## 4. Outstanding — the largest gap in the program

The research pass on this subsystem delivered its radio sections and then was lost to a rate limit before sending the rest. Still needed, and all of it cheap to resume because the sources are known:

- **The transition parameter set in depth** — `Q_ASSIST_ANGLE`, `Q_ASSIST_ALT`, `ARSPD_FBW_MIN`, `Q_TRANSITION_MS`, `Q_TRANS_DECEL`, and the full `Q_OPTIONS` bitmask.
- **`VTOL_TAKEOFF` and `VTOL_LAND` mission commands, and `LOITER_TO_ALT`** — how they sequence, and how they interact with the 150 ft altitude floor.
- **`Q_LAND_FINAL_ALT` and `Q_LAND_SPEED`**, and the QLAND and QRTL descent profile.
- **TECS tuning for a quadplane specifically**, and **L1 versus NPFG** as the navigation controller.
- **`NAVL1_PERIOD` and `NAVL1_DAMPING` against our turn radius**, with published waypoint-tracking error for small fixed-wing UAS. This is the quantitative half of section 1.
- **Documented quadplane failure modes with mechanism and mitigation** from the ArduPilot forum and GitHub issues — failed forward transitions, back-transition and landing failures, airspeed sensor failures, EKF failsafe and GPS glitch behaviour in a quadplane, lift-motor ESC desync, and vibration and compass issues from boom-mounted lift motors.
- **Failsafe cases that fired wrongly or not at all**, as design evidence.
- **GNSS and compass on a carbon airframe** — multi-band GNSS, dual GPS blending, moving-baseline yaw — and the RTK verdict. Note that [payload delivery](payload-delivery.md) already concludes RTK is not worth buying for delivery accuracy; this would confirm or contradict that for navigation.

**One thing to verify ourselves rather than read:** whether the judges score waypoint accuracy from our telemetry or their own tracking, and what they accept as evidence. It changes how we instrument the aircraft, and it is worth asking in writing.
