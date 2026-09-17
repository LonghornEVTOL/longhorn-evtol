# Electrical Onboarding: the Power Board Ladder

Part of [Longhorn eVTOL onboarding](README.md), which runs by invitation. Everything you need is in this repository, and the first three weeks need nothing but a laptop.

Every new Electrical member builds the same board first, then redesigns it. Level 1 teaches the design-to-bring-up flow on a proven circuit. Level 2 forces the fundamentals the club's complex boards depend on: each required change maps to a concept used later in the pack switch board, BMS, custom ESC, and flight controller.

Teaching happens before each stage: the officer running onboarding gives a short lesson and the listed resources, then pairs build it. Resource slots below are placeholders to fill in. Part numbers are suggestions to verify for stock and price before ordering.

Engineering references live beside this file: [PCB design guide](../hardware/pcb-design-guide.md), [avionics parts](../vehicle/avionics-parts.md), [teams and projects](../teams.md), [safety requirements](../safety/vehicle-safety-requirements.md).

---

## Before you start

- [ ] Read the [git workflow](git-workflow.md) and get the repository cloned
- [ ] Install KiCad with the club library from [`hardware-lib/`](../../hardware-lib/)
- [ ] Install [LTspice](tools/ltspice.md) and run **Tools → Sync Release**
- [ ] Skim the [safety requirements](../safety/vehicle-safety-requirements.md) and the [1/3-scale demonstrator design](../vehicle/subscale-demonstrator.md)
- [ ] Members only: safety onboarding with an officer, required before any bench work or lab access

---

## Level 1: 6S to 5 V buck converter (weeks 1 to 3)

**Build a textbook buck converter from the regulator's reference design, and understand every part on it.**

| Spec | Value |
| --- | --- |
| Input | 18 to 25.2 V (6S Li-ion or LiPo), bench supply for testing |
| Output | 5.0 V, 3 A continuous |
| Regulator | TI LMR33630 (3.8 to 36 V, 3 A synchronous buck) or equivalent; verify stock |
| Board | 2-layer, 50 × 50 mm or smaller, XT30 input, JST-GH or screw-terminal output, test points on input, switch node, and output |
| Extras | Input fuse, power LED |

**Lessons before starting** (resources: _to be added_)

1. Ohm's law, Kirchhoff's laws, power, and why voltage dividers set output voltage
2. Inductors and capacitors in switching circuits: energy storage, ripple
3. How a buck converter works: switch, inductor, diode or synchronous FET, duty cycle ≈ Vout / Vin
4. Reading a datasheet: absolute maximum ratings, typical application circuit, layout guidelines
5. Simulation: what SPICE actually solves, and why a simulation is a check on your math rather than a replacement for it

**Calculation worksheet** (hand calcs first, then check against TI WEBENCH or the datasheet)

- [ ] Feedback divider for 5.0 V from the regulator's reference voltage
- [ ] Duty cycle at 18 V and 25.2 V
- [ ] Inductor value from allowed ripple current (target 20 to 40% of 3 A), and its saturation current rating
- [ ] Output capacitance for a ripple target (for example 50 mV peak-to-peak), including ceramic capacitor DC bias derating
- [ ] Input capacitance and its RMS current rating
- [ ] Estimated efficiency and regulator temperature rise at 3 A

**Simulation lab (LTspice)** — setup and step-by-step instructions are in [tools/ltspice.md](tools/ltspice.md). Do the worksheet first, then simulate; the simulation is how you find out your math was wrong before the board is fabricated, not a substitute for the math.

- [ ] **Simulation A, open-loop buck built from parts** (ideal switch, diode, your L and C): confirm the duty cycle, inductor ripple current, output ripple, and efficiency against the worksheet, and explain every disagreement
- [ ] Sweep it: halve the inductor and watch ripple double; add capacitor ESR and watch ripple jump; drop to a 100 mA load and find discontinuous conduction; look at the startup inrush
- [ ] **Simulation B, closed loop with a real regulator** (Analog Devices LT8640S, since TI ships no LTspice model for the LMR33630): startup, line step, and a 0.5 to 2.5 A load step, with overshoot and recovery time written down
- [ ] Predicted vs simulated table filled in, plots saved as PNG, `.asc` files committed in `sim/`

**Layout lesson:** the hot loop (input capacitor, switch, ground) kept tiny; switch node copper kept small; feedback trace away from the inductor; solid ground plane.

**Deliverables and gates**

| Week | Deliverable | Gate |
| --- | --- | --- |
| 1 | Worksheet, LTspice simulations A and B with plots, schematic in KiCad using the club library | **Design review**: hand calcs and simulation agree (or the difference is explained), ERC clean, every calc justified, parts in stock |
| 2 | Layout following the datasheet layout guide | Layout review: DRC clean, hot loop checked; group fab order |
| 3 | Hand assembly and bring-up | **Measurement lab** below, written up against the simulated numbers |

**Measurement lab** (oscilloscope, electronic load or power resistors, current-limited supply)

Every measurement gets compared against both the hand calculation and the simulation. Three columns that disagree is the most useful thing you will produce this level.

- [ ] Output voltage at 0, 1, 2, and 3 A: compare with the divider calculation
- [ ] Output ripple with a short ground spring on the probe: compare with the capacitor calculation
- [ ] Switch node waveform: measure duty cycle at 18 V and 25.2 V
- [ ] Efficiency (Pout / Pin) at 0.5, 1.5, and 3 A: compare with the estimate
- [ ] Load step 0.5 → 2.5 A: overshoot and recovery time
- [ ] Regulator and inductor temperature after 10 minutes at 3 A

---

## Level 2: redesign it (weeks 4 to 8)

Take the working Level 1 design and turn it into one of the boards below. Every required feature exists to teach a fundamental; each pair explains the concept at the design review before it goes in the schematic.

### Option A: Protected, monitored avionics power module

The Level 1 buck becomes the subscale demonstrator's avionics power board: it survives mistakes, knows when the battery is low, and reports what it's doing.

| Required feature | Fundamental it teaches | Where the club uses it later |
| --- | --- | --- |
| **Reverse-polarity protection** with a P-channel MOSFET (ideal-diode style) | MOSFETs as switches: Vgs threshold, Rds(on), body diode, conduction loss | Pack switch board back-to-back MOSFETs |
| **Undervoltage lockout with hysteresis** using the regulator's enable pin and a resistor divider, so the board turns off below about 19 V and back on above about 20 V | Dividers, thresholds, and why hysteresis prevents chatter | Pack switch UVLO, BMS cutoffs |
| **Low-battery warning** with a comparator (e.g. LM393) and positive feedback driving an LED and an open-drain output for the flight controller | Comparators, hysteresis math, open-drain outputs, pull-ups | Safety monitor limit checks |
| **Output current sensing** with a small shunt and a current-sense amplifier (e.g. TI INA180), plus an RC filter into an ADC header | Op-amp gain, shunt sizing vs power loss, RC low-pass filters and cutoff frequency | Motor current telemetry, pack switch current sense |
| **Electronic overcurrent shutdown**: the comparator trips the enable pin above 3.5 A and latches until the input is cycled | Comparator plus latch logic, fault handling, why hardware protection must not depend on software | Pack switch short-circuit trip, E-stop philosophy |
| **Digital telemetry**: TI INA226 reading input voltage and output current over I²C to a header | I²C, addresses, pull-up sizing, calibration | Flight controller sensor buses, power monitor node |
| **Input transient protection**: TVS diode and input capacitor sizing for hot-plugging a battery | Inductive spikes, TVS standoff vs clamp voltage | Every board on the vehicle |

**Extra measurement lab:** hot-plug a charged pack with a scope on the input; sweep input voltage to find the UVLO thresholds; reverse the input on a current-limited supply; trip the overcurrent latch with an electronic load; calibrate the INA226 against a meter.

### Option B: Constant-current strobe light driver

The Level 1 buck regulates **current** instead of voltage to drive high-power LEDs for the demonstrator's anti-collision strobe.

| Required feature | Fundamental it teaches | Where the club uses it later |
| --- | --- | --- |
| **Current-mode feedback**: sense LED current with a shunt, amplify with a current-sense amplifier (e.g. TI INA180), and feed the regulator's feedback pin | Feedback control: what the regulator regulates, amplifier gain to cut shunt loss | Every regulated power stage |
| **Open-LED overvoltage protection** with a Zener or comparator on the output | What happens when a feedback loop loses its load | ESC and power stage fault cases |
| **PWM flash pattern** with a logic-level N-MOSFET (e.g. AO3400A) switching the LED string, driven by a microcontroller | MOSFET gate drive, switching speed, why gate resistors exist | ESC gate drive, contactor drivers |
| **Loop stability check** by stepping the LED current and watching overshoot | Stability, phase margin intuition, compensation | Motor control loops, DC-DC design |
| **Thermal design**: LED and regulator heat spread through copper pours | Power dissipation, thermal resistance, copper as a heatsink | Pack switch and ESC thermal limits |
| **Brightness telemetry**: measured LED current to an ADC header | ADC scaling, filtering | Telemetry everywhere |

**Extra measurement lab:** LED current accuracy at three set points; flash timing on the scope; loop step response; open-LED fault test; LED and board temperature after 10 minutes.

### Level 2 gates

Every required feature is simulated before it enters the schematic; the per-option simulation list is in [tools/ltspice.md](tools/ltspice.md#level-2-simulations). Protection and threshold circuits especially: a UVLO that chatters or a latch that does not latch is cheap to find in LTspice and expensive to find on a board.

| Week | Deliverable | Gate |
| --- | --- | --- |
| 4 | Concept note: block diagram, each feature's calculation and simulation, the fundamental it demonstrates | **Concept review**: the pair explains every feature and shows the simulation behind it |
| 5 | Schematic | Schematic review |
| 6 | Layout; group fab order | Layout review |
| 7 to 8 | Assembly, bring-up, measurement lab, and a short presentation of results vs predictions | **Final review**; the board moves to its project folder and onto the demonstrator bench |

**Lessons before Level 2** (resources: _to be added_): MOSFETs as switches; comparators and hysteresis; op-amps and current sensing; RC filters; feedback loops and stability; I²C; thermal basics; transient protection.

## After the ladder

Members who finish Level 2 are ready for the real boards on the [teams page](../teams.md): pack switch board, BMS, power monitor node, sensor nodes, and eventually the flight controller and custom ESC. Experienced builders can skip straight to Level 2 or to a real board after a quick Level 1 design review.

Other small boards from the teams page (PWM servo tester, harness continuity tester, E-stop test box, arming status light, sensor breakouts) remain good extra practice and pair well with Software projects.
