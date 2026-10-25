# Test and verification

**Status:** Research complete, v1. Applies across every subsystem. The stage-gate ladder in section 6 is the authoritative flight-test progression for the competition aircraft and for the trainer.

**Why this document exists.** The 2026 SUAS results report **55 flights and 21 crashes** across 64 teams — 38% per flight — and a crash costs 50% of demonstration points, uncapped. The single highest-return engineering activity available to this club is not making the aircraft better. It is making it not crash. That is a test-discipline problem, and it is almost entirely free.

---

## 1. What the evidence actually supports, stated honestly

**There is no study showing that teams who simulate crash less.** Not for university competition teams, not for industry. If anyone claims otherwise, ask for the citation. What follows is what the evidence does support, so the club can make the argument in its technical design report without overstating it.

**Simulation finds the overwhelming majority of real defects in this exact stack.** A study that mined ArduPilot's own git history for bug-fixing commits and characterised 228 bugs by what it takes to trigger them found:

| Question | Result |
| --- | ---: |
| Bugs requiring **physical hardware** to trigger or observe | **10 of 228** |
| Bugs requiring **concurrent events** | 13 of 228 (5%) |
| Bugs requiring **environmental factors** | 22 of 228 |
| Bugs depending on a **particular configuration** | **81 of 228** |
| Bugs triggered during **normal operation** | 184 of 228 (about 80%) |
| Bugs needing continuous RC input | 47 of 212; **165 do not** |
| Bugs fixed by modifying a single file | 183, median diff **5 lines** |

The authors' own framing is worth quoting: their intuition was that most real-world bugs would be impractical to reproduce in simulation, and they were "pleasantly surprised." Their stated corollary matters for how the club runs SITL: because more than a third of bugs are configuration-dependent, **the value is in sweeping configurations, not in flying one mission once.**

The honest caveat, which the authors state themselves: it was unclear for many bugs whether they were found in the field or in simulation. So this is not a measurement of bugs-caught-in-sim-instead-of-in-flight.

**Simulation-based testing demonstrably finds previously unknown bugs in ArduPilot.** One fuzzing study validated against ArduPilot, PX4 and Paparazzi discovered **156 previously unknown bugs in 48 hours, of which developers confirmed 106**, and 128 of the 156 could not have been found by prior tools. An earlier tool from the same group reported 89 input-validation bugs, 87 of them zero-day, though with a much weaker confirmation rate.

**The leading cause of bug-induced UAS crashes is mishandled sensor readings** — and the same study found that source repositories *under-report* sensor bugs relative to what simulation reveals. A separate taxonomy of 569 PX4 and ArduPilot bugs classifies them into eight types, two of which are *parameter* and *limit*, matching the configuration finding above.

**ArduPilot gates every commit behind 90 simulated quadplane scenarios** (section 3). One of them is docstringed as a regression test for a specific numbered issue.

**So the defensible claim is:** about 95% of historical ArduPilot bugs need no hardware and no concurrency to reproduce; simulation has found hundreds of real defects in this stack; the defect population is dominated by sensor handling and configuration, which is exactly what a parameter-sweeping SITL campaign exercises; and the upstream project itself will not merge a commit that fails its simulated quadplane suite. **"Simulation prevents crashes" is not defensible. This is.**

---

## 2. SITL for quadplanes, and what it does not model

`sim_vehicle.py -v ArduPlane -f quadplane --console --map` is the right invocation. Of the thirteen quadplane frames in the tree (`quadplane`, `-tilt`, `-tri`, `-tilttri`, `-tilttrivec`, `-tilthvec`, `-cl84`, `-ice`, `-can`, `-PPP`, `-copter_tailsitter`, plus `plane-tailsitter` and `firefly`), plain `quadplane` is a quad-X lift set plus a forward pusher — our configuration. Ports: SERIAL0 on TCP 5760, SERIAL1 and SERIAL2 on 5762 and 5763 (use these for the companion computer, because it mirrors the real wiring), MAVLink to the GCS on UDP 14550 and 14551.

### Read this before trusting a SITL result

The simulated quadplane computes fixed-wing forces, then multirotor forces, and **adds them**. The fixed-wing half is a single linear-derivative model with coefficients hard-coded from a Skywalker 2013 foamie: wing area 0.45 m², span 1.88 m, chord 0.24 m. A source comment admits the CG offset "should be -0.02, but that makes the plane too tail heavy in manual flight. Adjusted to -0.15 gives reasonable flight."

So out of the box you are flying a 1.88 m foamie with a quad bolted on. Specifically **not modelled**, verified in source:

| Not modelled | Why it matters to us |
| --- | --- |
| **Prop wash over the wing and control surfaces** | The only exception in the code is tailsitter-only. For a conventional quadplane, control effectiveness is a pure function of free-stream airspeed |
| **Lift-rotor and wing interference** | No rotor download on the wing, no blockage of rotor inflow, no change in aero coefficients when the lift rotors run |
| **Thrust versus inflow velocity** | Momentum drag is modelled as isotropic, and the source says so. Translational-lift effects during transition are crude |
| **Forward-motor electrical load** | Hard-coded: `// assume 20A at full fwd throttle`. **The transition current spike — the thing that actually kills quadplanes — is a constant 20 A** |
| **Mass** | `SIM_FRM_MASS` defaults to 3.0 kg and the quadplane model scales it 1.5×, so about 4.5 kg against our 6.8 kg |
| Anything structural | No airframe flex, no CG migration, no ESC desync, no servo stall |

**The single highest-value simulation action available to us** is therefore to build a custom aero file and load it with `-f quadplane:longhorn.json`, from our own geometry and AVL/XFLR5 or wind-tunnel derivatives, and to set `SIM_FRM_MASS`, `SIM_FRM_DIAG_SZ`, `SIM_FRM_HOVR_THR`, `SIM_FRM_EXPO` and the battery terms from thrust-stand data. The frame string parser already supports it and a worked example ships in tree. **Without that, SITL verifies our logic and our mission, not our vehicle. Say so in the report rather than claiming fidelity we do not have.**

### Failure injection, which is the real reason to run SITL

This list is why section 1's configuration finding matters. All of it is available:

- **Airspeed**, the quadplane killer: `SIM_ARSPD_FAIL`, `SIM_ARSPD_PITOT` (blocked pitot), `SIM_ARSPD_SIGN` (reversed pitot/static), `SIM_ARSPD_RATIO`, `SIM_ARSPD_RND`, `SIM_ARSPD_OFS`, plus a second sensor
- **Propulsion:** `SIM_ENGINE_FAIL` (motor mask) with `SIM_ENGINE_MUL` (partial or total lift-rotor loss)
- **Power:** `SIM_BATT_VOLTAGE`, `SIM_BATT_CAP_AH`, **`SIM_BATT_RES_OHM`** — transition sag, directly
- **Wind:** `SIM_WIND_SPD`, `_DIR`, `_DIR_Z`, `_TURB`, `_TC`, `_T` (shear profile), `_T_ALT`, `_T_COEF`
- **Upset:** `SIM_SHOVE_X/Y/Z/TIME` and `SIM_TWIST_X/Y/Z/TIME` for impulsive gusts
- **Sensors:** `SIM_MAG_ALY` and `SIM_MAG_MOT` (motor-current magnetic interference), `SIM_ACCEL*_FAIL`, `SIM_VIB_*`, `SIM_GPS*`, and **`SIM_IMU_ORIENT` — a mis-oriented flight controller, a documented real crash cause.** Prove our pre-arm checks catch it
- **Link:** `SIM_RC_FAIL`

### External simulators, triaged

| Backend | Verdict |
| --- | --- |
| **Built-in** `-f quadplane` | **Primary.** Fast, headless, CI-able, full failure injection |
| **JSON** backend | Highest-fidelity path if we build a Simulink or in-house 6-DOF model. Backend can live on another machine |
| **ROS 2 + Gazebo** (`ardupilot_gz`) | **Most directly relevant external stack — a lift-plus-cruise quadplane example ships with it.** Needed if the Jetson has to see camera input |
| Gazebo standalone | Usable, no ROS dependency, camera via a plugin to UDP. Needs hardware OpenGL |
| RealFlight / FlightAxis | Good for hand-flown transition familiarisation and pilot training. Windows-only, RealFlight 8 or 9 only, needs over 200 graphics FPS or flight behaviour degrades. Not CI-able |
| **AirSim** | **Do not use.** Archived upstream; ArduPilot's own page says the instructions are no longer maintained or tested |
| X-Plane, FlightGear as HITL | **Archived.** See section 4 |

Pre-built airframe models live in `ArduPilot/SITL_Models`. The Gazebo `alti_transition_quad` model has links `motor_1` to `motor_4` plus `motor_f` — **exactly a four-lift-rotor plus single-pusher layout, the closest shipped analogue to our aircraft**, albeit at 37.7 lb rather than 15.

---

## 3. Continuous integration, and the mission as a regression test

ArduPilot's autotest framework runs `sitltest-quadplane-tests1a`, `1b` and `1c` — **37, 7 and 46 quadplane test cases** — in containers on every push to every branch and on every pull request, uploading failure artifacts and BIN logs when they fail. QuadPlane tests always start at a fixed location, disarmed, with RC zeroed.

The tests that map onto our crash risk: `QAssist`, `TransitionMinThrottle`, `BackTransitionMinThrottle`, `mission_MAV_CMD_DO_VTOL_TRANSITION`, `CruiseRecovery`, `QLoiterRecovery`, `FastInvertedRecovery`, `AHRSFlyForwardFlag`. Plus a full family on landing and failsafe (`QRTLGradualAltDescent`, `LoiterAltQLand`, `VTOLLandSpiral`, `VTOLLandGoAround`, `PlaneWindFailsafe`, `MidAirDisarmDisallowed`), on estimation (`WindEstimateConsistency`, `AHRSSwitchBackendResets`, `GyroFFT`, `SimBatteryResistance`), and on missions and fences (`Mission`, which flies a two-leg course with a fence loaded, and a complete `FenceRelativeTo{Home,Origin,AMSL,Terrain}{Min,Max}Alt` set).

**Two of these are worth reading before writing our own**, because they show how to assert on signals rather than vibes:

`QAssist` takes off to 50 m, transitions, **kills the forward motor** and asserts assist engages by waiting on a servo channel value. Then for each axis it **pins the control-surface servo to a dead value**, commands hard opposite stick, and asserts both that the lift motors spin up and that a specific status text appears. Then it sets a transition-failure action and asserts the aircraft goes to QRTL.

`CruiseRecovery` uses `SIM_APOS_*` to **spawn the vehicle mid-air in a defined upset** — 70° nose down, 150° roll, 40 m/s — force-arms, and asserts recovery **20 times in a row**, each time requiring level attitude within 10 s to a 10° tolerance, airspeed within ±1 m/s of cruise, and a final height of at least 100 m. `FastInvertedRecovery` fails if altitude lost during recovery exceeds **3 m**.

### What we should build

**Our competition mission, our fence, our `DO_LAND_START` sequence and our failsafe actions should be a CI test that runs on every configuration change.** The shipped `Mission` test is a direct template. The pattern:

1. Subclass the quadplane test suite, register tests in a `tests()` list.
2. `load_mission()`, `load_fence()`, `wait_ready_to_arm()`, `arm_vehicle()`, `change_mode('AUTO')`, `wait_waypoint(a, b, max_dist_to_final_wp_m=...)`, `wait_disarmed()`.
3. Assert on signals with numeric bounds — `wait_servo_channel_value`, `wait_statustext`, `wait_attitude`, `wait_airspeed`, `wait_altitude` — raising on a specific number, the way `FastInvertedRecovery` uses 3 m.
4. Wrap every parameter change in a context push and pop so tests cannot poison each other.
5. Drive our own failure cases from the section 2 list: a lift-rotor out during transition, a blocked pitot on the transition leg, battery sag, worst-case competition wind, lost link at the far end of the course, and a mis-mounted flight controller.
6. Run it all in GitHub Actions on every PR. Keep the framework's bisect helper for the day a regression appears — `git bisect run` against a named test works out of the box.

---

## 4. Hardware in the loop is dead; use simulation on hardware

ArduPilot's HITL page carries an "Archived, no longer supported" banner and covers only X-Plane and FlightGear. The reason given by developers is that the EKF cannot be fed fast or consistently enough over USB.

**The supported replacement is "Simulation on Hardware":** the vehicle's own autopilot runs both the flight software *and* ArduPilot's native physics. Build with the in-tree script, passing `--simclass QuadPlane`; set the start position with `SIM_OPOS_*`. Pre-built firmware exists only for one board, so a Pixhawk 6X quadplane build is ours to compile.

What it buys: validation of **our actual board, wiring harness, servos and control-surface travel, telemetry link and Jetson connection** against simulated physics. What it does not buy: external high-fidelity aerodynamics.

**Two warnings, both from the official page.** First, verbatim: "Parameter space is shared between the real aircraft and the simulated aircraft. Thus parameters must be wiped and rewritten to the vehicle when moving between real and simulation firmware." **Our tuned competition parameter set will not survive the round trip — back it up and plan the wipe.** Second: "Remove/disable all props or other dangerous actuators before continuing."

PX4 supports both HITL and on-board simulation natively. That is a reason to know the difference, not a reason to switch stacks — it would cost us ArduPilot's quadplane maturity.

**Recommended layering:** built-in SITL in CI for logic and mission; a custom-coefficient JSON or Gazebo model for vehicle behaviour; simulation-on-hardware on a bench airframe with props removed for harness and link validation; and a tied-down ground run with props on as the last gate before flight.

---

## 5. The two published stage-gates worth copying

### NASA LA-7: a 4-foot-span surrogate, seven stages

A 50% scale testbed flown to build confidence in a configuration and to train crew at a lower investment level. The progression, verbatim:

1. Single motor thrust stand testing
2. Full model 8-motor static thrust testing
3. Pitch axis tuning stand
4. Indoor tethered hover testing
5. Indoor free flight hover testing
6. Outdoor free flight hover testing
7. Outdoor free flight transition testing

Stage 3 is a stand with a rotation axle **at the CG**, used to tune auto-levelling and pitch-rate gain in both hover and forward-flight configurations by perturbing with a stick. The authors are candid that it cannot achieve wind-on conditions but that "it provided a best guess at a starting point for gain settings."

Stage 4 is itself four sub-steps: suspended **slightly below hover thrust** with a bridle allowing **yaw only**; then a bridle allowing pitch, roll and yaw; then tethered **at** hover thrust; then free flight. Battery characterisation at this stage gave about 5 min hover with 20% reserve, and **an IR camera measured motor housing temperatures.**

Stage 7 proceeded through transition in **5% increments**, establishing straight and level flight at each point before tuning and advancing.

Their lessons learned, which transfer directly:

- Select propellers suitable for **both** hover and forward flight
- **Hovering in winds above 5 knots is difficult due to large wing area normal to wind**
- **Tethered hover testing and 1-degree-of-freedom pitch rigs speed tuning while limiting risk to the vehicle**
- Transition testing should proceed in small increments with tuning that allows **hands-off straight and level flight at each transition point**
- **Propulsion forces dominate control-surface forces. Solve control and damping issues starting with the motors and propellers**

### NASA GL-10: phase-gated releases, and three dead surrogates

A clean-sheet VTOL on about $1.8M over two years — "a low funding level" — with accepted risks named explicitly, including that "control system architecture and gains were developed during flight test." Three airframes in ascending cost: a 6 lb foamie to test avionics and software functionality only; a mid-tier surrogate used as "a flying simulator"; and the $300k aircraft itself.

**The gate mechanism is the part to copy.** Verbatim: "This build up approach mitigated risk for the [safety review board]. **A flight safety release was issued one phase at a time. When all of the test objectives of one phase was finished, the project returned to the [board] for the flight safety release for the next phase.**"

**The known-condition rule, inside a phase:** "The flight would begin with the **known condition, usually vertical takeoff and hover**, move toward the edge of the currently tested envelope and **back to the known condition for land. When aborts were required, the pilot immediately returned to the known condition.**"

**Avionics screening:** every board was serialised and flight tested on a surrogate before being used on the expensive aircraft, and every software version was flight tested on a surrogate first.

**Tethering:** from above on a belay line, **attached through the CG to minimise the moments imparted to the aircraft by the tether.**

**Daily preflight:** an operations procedures document covering meteorological go/no-go criteria approved by the safety board through to emergency procedures, and a checklist covering "structural components that weaken with flight time to avionics functionality checks." It took **about one hour each morning and was signed by the test engineer** so the record would exist in the event of an accident investigation.

### The crash story, and the reason it is in this document

**Three of five surrogate aircraft were lost**, and the first two investigations reached confident wrong conclusions.

Crash 1: video showed the horizontal tail rotating toward forward flight; a servo linkage was found separated. "It was thus wrongly deduced this was the cause of the crash." Crash 2: aggressive roll and departure, no response to input, thermal damage on the kill switch. "It was wrongly deduced the kill switch was the cause" and kill switches were removed from the fleet. Crash 3: a full backflip seconds after takeoff, and **nothing out of place on inspection.** Video showed the tail rotating to forward-flight orientation and then returning to correct orientation about 4 seconds after the rotation — and **the avionics boot time is about 4 seconds.**

**Root cause of all three: the avionics bus dropping below 4.6 V and restarting.** An oscilloscope showed the nose wheel servo intermittently pulling the 5.1 V bus down 0.3 V, and full power pulling it down another 0.2 V. The combination triggered the restart. **Fix: all servos on a separate bus from the avionics, and the project never had the problem again.**

Three lessons for a 15 lb quadplane: physical evidence at the crash site produced two confident wrong answers, and the right answer came from correlating video timing against known boot time. An intermittent power-integrity fault flew successfully many times between crashes, so **"it didn't crash" is not evidence of health.** And **put the servos on a separate power bus from the flight controller, and log bus voltage** — ArduPilot gives this for free as `POWR.VCC`, where 0.10 to 0.15 V variation is documented as normal, which is to say GL-10's killer sat right at the edge of "normal."

One more GL-10 finding relevant to lift-plus-cruise at our scale: do not rely on slipstream over control surfaces for low-speed yaw control. Their ailerons were already 30% chord and full span and still could not react to gusts in hover, because at small scale the prop disk loading is lower and the wing loading is lower.

---

## 6. The stage-gate ladder for our aircraft

Assembled from LA-7's seven stages, GL-10's phase-release and known-condition rule, and ArduPilot's own 13-step VTOL tuning process. **No gate is skipped, and no gate is entered until the previous gate's exit criteria are all met and signed.**

| Gate | Content | Exit criterion |
| --- | --- | --- |
| **G0 Bench** | Fixed-wing setup; ESC calibration; bench accel calibration; **motor alignment measured prop-to-table**; motor numbering and direction via the GCS motor test; X versus H frame by geometry; **servos on a separate power bus from the FC**; `POWR.VCC` logged | Alignment within about 1°; motor order and direction verified; bus sag under 0.1 V at full servo load |
| **G1 Thrust stand** | Single motor, then all four static. Derive `Q_M_THST_EXPO`, `Q_M_PWM_MIN/MAX`, `Q_M_SPIN_ARM/MIN`, hover current, and the `SIM_FRM_*` values | Measured hover thrust at least 2× weight; expo from data, not default |
| **G2 Simulation** | Custom aero JSON and real `SIM_FRM_*`; SITL mission, fence, `DO_LAND_START`, failsafes; the section 2 failure injections; all of it in CI | Full competition mission flies unattended in SITL; every injected failure ends in a safe state |
| **G3 Sim on hardware** | Props off. Validates harness, servo travel, mode switching, Jetson link, telemetry | Correct surface deflections in every mode; Jetson sees the stream; **then wipe and rewrite the real parameter set** |
| **G4 1-DOF rig** | Pitch axle **at the CG**; perturb with a stick; tune auto-level and rate damping in hover and cruise configurations | Well-damped pitch response at multiple transition settings |
| **G5 Tethered hover** | Belay line from above, **attached through the CG**; LA-7's four sub-steps, yaw-only then 3-axis then at hover thrust then released. `Q_M_HOVER_LEARN` = 2 | No oscillation; **`RATE.YOut` under 0.1 in calm air**; `Q_M_THST_HOVER` learns to 0.3 to 0.6; VIBE under 30 m/s²; clipping at zero |
| **G6 Free hover** | ArduPilot steps 5 to 11: QSTABILIZE **arm then immediately disarm to test the disarm procedure**; 1 m hover with 5° inputs; QSTABILIZE to 5 m then QHOVER for at least 30 s; **2 min no-input hover for the notch filter**; QUICKTUNE in QLOITER at 3 m; then 5° inputs escalating to full deflection | Clean tune, log-reviewed; the altitude controller proven in flight **before** any QHOVER takeoff |
| **G7 Fixed wing** | FBWA only, hand flown. **Autocal the airspeed sensor on this flight**, then run MAGFIT | Airspeed ratio converged; `SVT` test ratio under 1; DCM and EKF3 wind estimates agree |
| **G8 Transition at altitude** | At least 100 m AGL, safety pilot on sticks, QHOVER one switch away. LA-7's increments, with **hands-off straight and level established at each step.** GL-10's rule: start at the known condition, go to the edge, return; on any abort return immediately | Hands-off level flight at each step; no divergent oscillation; clean back-transition |
| **G9 Auto mission** | AUTO last. `DO_LAND_START` plus `RTL_AUTOLAND`; **base and final legs sized for over 100 m turn radius at cruise**; `TECS_LAND_ARSPD` just above `AIRSPEED_MIN`, itself 25% above stall | Full mission flown; approach repeatable in wind |

Plus two process rules borrowed wholesale: **a written daily preflight checklist with meteorological go/no-go criteria, signed by one named engineer**, and **a gate-by-gate release** where a two-signature rule stands in for GL-10's institutional safety board.

### ArduPilot's own numbers, for the tuning gates

Setup before first flight: `Q_M_THST_EXPO` 0.55 for 5-inch props, 0.65 for 10-inch, 0.75 for 20-inch and up, and **do not exceed 0.75 without thrust-stand data**. `INS_GYRO_FILTER` 80 Hz for 5-inch, 40 for 10-inch, 20 for 20-inch. `Q_A_ACC_P_MAX` and `_R_MAX` 1100 for 10-inch, 500 for 20-inch. `Q_A_THR_MIX_MAN` 0.1 and `Q_M_THST_HOVER` 0.25 or lower. **Perform the initial tune at minimum takeoff weight with full batteries.** Fly in low wind at 15 to 25 °C. Configure and test an arm/disarm switch first.

Manual tuning order: filters before D terms; roll before pitch; per axis raise D by 50% until oscillation, back off 10%, then a further 25%, then the same for P, setting I equal to P each time. Properly set notch filters allow D increases of up to 10×.

Reliability items that read as a hard-won post-incident checklist: autocal the airspeed sensor on the first fixed-wing flight; run MAGFIT after the first VTOL, fixed-wing and VTOL flights; **landing legs long enough to correct turn overshoot before transition** — at 30 m/s with 30° bank the turn radius exceeds 200 m, and at our 22 to 25 m/s it is still well over 100 m; `Q_ASSIST_SPEED` about 3 m/s below `AIRSPEED_MIN` but above stall; **set `Q_TRANS_FAIL` and `Q_TRANS_FAIL_ACT` to QRTL**; and ArduPilot's own instruction to **test the landing sequence in SITL using the `-f quadplane` frame with our TECS settings and limits.**

Mechanical items that matter more than tuning: check motor alignment on the bench by measuring prop-to-table distances, because **even a degree or two of misalignment causes significant yaw trim problems**; choose X versus H mixing by geometry, because the wrong scheme lets structural twist cancel yaw input; do the level calibration with the aircraft in its **cruise attitude**, and prefer mechanically tilting the VTOL motors 3 to 5° forward; motor outputs run at 400 Hz, so **most analog servos sharing a timer group will overheat or fail**; and **battery sag during transition is a real risk with high-capacity low-C packs** — set `BATT_WATT_MAX`, `Q_M_BAT_CURR_MAX` and `Q_M_BAT_VOLT_MIN`.

---

## 7. Log review, as a gate rather than a post-mortem

**The rule: no next flight until the previous flight's log has been reviewed against this checklist.** GL-10 is the cautionary case.

| Signal | Healthy | Action threshold |
| --- | --- | --- |
| `VIBE` VibeX/Y/Z | Typically under 15, peaks to 30 m/s² | **Over 30 investigate. Over 60 nearly always breaks position or altitude hold** |
| `VIBE` Clip0/1/2 | Ideally zero for the flight | Under 100 acceptable on a hard landing. **A count that keeps rising is a serious problem** |
| `ATT` DesRoll vs Roll, DesPitch vs Pitch | Tracking | **A sudden divergence suggests mechanical failure** |
| `POWR.VCC` | **0.10 to 0.15 V variation is normal** | Larger swings mean power ripple. A log that ends abruptly while airborne suggests a brownout |
| EKF innovations `IVN/IVE/IVD`, `IPN/IPE`, `IPD`, `IMX/Y/Z`, **`IVT`** | GPS velocity about ±0.3 m/s, GPS position about ±0.5 m, baro about ±0.15 m with 2 m transients normal, mag under about ±50 | **`IVT` airspeed innovations should centre on zero — a constant offset is a steady airspeed error** |
| EKF test ratios `SV`, `SP`, `SH`, `SMX/Y/Z`, **`SVT`** | Occasional spikes above 0.5 normal | **1 is the limit.** Above 1 with good GPS means the filter stopped using that data. Sustained `SM*` above 1 is a compass calibration or installation problem — fix it, do not widen the gate. Persistent `SVT` above 1 means airspeed calibration error |
| `ERR` Subsys/ECode | None | 3 compass, 11 GPS glitch, **16 EKF check bad variance, 17 EKF failsafe**, 18 barometer, 19 CPU load watchdog, 29 vibration failsafe |
| `mag_field` versus throttle | Under 30% fluctuation | 30 to 60% grey zone. **Over 60% is really bad interference.** Normal field length about 330 |
| `GPS.HDop` | Under 1.5 very good | Over 2.0 may mean poor positions. **Fewer than 12 satellites is bad** |
| **`RATE.YOut`** (quadplane-specific) | — | **Over 0.1 in calm air with no pilot input means yaw asymmetry** — motor misalignment, frame twist, or wing obstruction |
| `RCOUT` diagonal pairs in hover | Balanced | Large differences between diagonal pairs indicate yaw problems |

Also check "the leans" by comparing `AHRS2.Roll` against both EKF instances — they should agree within a few degrees. And diagnose vibration by axis: X and Y both high means motor bearings, prop balance or FC damping; one axis only means FC mounting, such as a wire restraining it; Z high means prop track or vertical motor play.

### Tools

**MAVExplorer**, which ships with MAVProxy, is the workhorse: `graph ATT.Roll ATT.Pitch`, `condition GPS.Spd>4`, `map GPS POS` coloured by flight mode, `param TECS_*`, and **`paramchange <regex>` to show parameter changes during the log**. Graph expressions are arbitrary Python. The FlightMode menu limits graphs to selected modes, which is invaluable for isolating a transition. **Custom graphs save to a shareable XML file — build a team-standard quadplane review graph set and commit it to the repo.** MAGFIT lives here too.

**UAV LogViewer** at `plot.ardupilot.org` does 3D flight replay, field search, saved presets and per-axis scaling, mostly computed in the browser. It performs no automated analysis.

**Mission Planner** does manual review and produces a KMZ for Google Earth. Download over MAVLink wants a high-speed port, typically 921600 baud.

**There is no "Auto Analysis".** Its documentation page returns 404 across every section of the site, and the current log-analysis page describes only manual review. **Do not build a review process around it. Build the checklist above instead, ideally as a script over the `.bin` so it runs the same way every flight.**

---

## 8. Three things to carry into every design review

1. **Our out-of-the-box SITL quadplane is a 1.88 m foamie at 4.5 kg with prop wash unmodelled and transition current hard-coded at 20 A.** It is excellent for logic, missions, failsafes, fences and configuration sweeps — where about 95% of historical ArduPilot bugs live, and where more than a third are configuration-dependent. It is weak for transition dynamics. Fix what is cheap: a custom aero JSON and real `SIM_FRM_*` values from the thrust stand.
2. **The two best published stage-gates for our exact problem are free PDFs.** Copy LA-7's seven stages and GL-10's phase-by-phase safety release with its known-condition abort rule and CG-attached belay tether, rather than inventing a campaign.
3. **GL-10 lost three of five aircraft to a 0.3 V servo-induced bus sag that rebooted the avionics in flight, after two confident wrong diagnoses.** Separate bus for servos, log and review `POWR.VCC` every flight against the 0.10 to 0.15 V band, and treat "it flew fine last time" as meaningless for intermittent faults.

---

## 9. Open items

- Build `longhorn.json` aero coefficients, from AVL or XFLR5 first and wind tunnel later. Blocked on the airframe geometry freeze.
- Write the competition mission as an autotest case and wire it into GitHub Actions. Not blocked on anything; can start now against a placeholder mission.
- Compile simulation-on-hardware firmware for the Pixhawk 6X with `--simclass QuadPlane`, since no pre-built image exists for it.
- Commit the standard MAVExplorer graph set for quadplane review.
- Pull the 2026 SUAS flight status spreadsheet. RoboNation publishes per-team flight and crash data, and **if it records causes it is the single most valuable document available to us, and nobody appears to have analysed it.**
- Ask Dr. Sirohi and Texas Aerial Robotics about institutional memory on flight-test practice and on the UT-area RF environment.
