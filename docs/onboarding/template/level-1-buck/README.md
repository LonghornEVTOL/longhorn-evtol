# Electrical Level 1: 6S to 5 V Buck Converter

Curriculum: [Level 1 in the onboarding guide](../../electrical.md#level-1-6s-to-5-v-buck-converter-weeks-1-to-3) · Simulation: [LTspice guide](../../tools/ltspice.md)

| Spec | Target |
| --- | --- |
| Input | 18 to 25.2 V |
| Output | 5.0 V, 3 A continuous |
| Regulator | TI LMR33630 or equivalent (record the final choice below) |
| Board | 2-layer, ≤ 50 × 50 mm, XT30 input, test points on input, switch node, output |

**Designer:** _name_ · **Reviewer:** _name_ · **Status:** not started

---

## 1. Design worksheet

Show the equation, your numbers, and the result. Check each against TI WEBENCH or the datasheet. Do this **before** you simulate.

| Item | Equation and inputs | Result | Datasheet / WEBENCH check |
| --- | --- | --- | --- |
| Regulator chosen | | | |
| Feedback reference voltage (from datasheet) | | | |
| Feedback divider (top / bottom resistor) | | | |
| Duty cycle at 18 V | | | |
| Duty cycle at 25.2 V | | | |
| Switching frequency | | | |
| Ripple current target (20 to 40% of 3 A) | | | |
| Inductor value | | | |
| Inductor saturation current rating needed | | | |
| Output capacitance for 50 mV ripple | | | |
| Output capacitor DC bias derating at 5 V | | | |
| Input capacitance and RMS current rating | | | |
| Estimated efficiency at 3 A | | | |
| Estimated regulator temperature rise at 3 A | | | |
| Input fuse rating | | | |

## 2. Simulation (LTspice)

Files in [`sim/`](sim/): commit the `.asc` schematics, the `.plt` plot settings, and a PNG of every plot you reference. Setup, directives, and the full experiment list are in the [LTspice guide](../../tools/ltspice.md).

### 2.1 Simulation A: open-loop buck, built from parts

| Quantity | Hand calculation | Simulated | Difference, and why |
| --- | --- | --- | --- |
| Output voltage at the calculated duty cycle | | | |
| Duty cycle needed for 5.00 V at 18 V | | | |
| Duty cycle needed for 5.00 V at 25.2 V | | | |
| Inductor ripple current (peak-to-peak) | | | |
| Inductor peak current | | | |
| Output ripple, ideal capacitor | | | |
| Output ripple with real ESR | | | |
| Efficiency at 3 A | | | |

Sweeps (say what you observed, in one line each):

- [ ] Inductor halved: ripple current _____
- [ ] Capacitor ESR set to 50 mΩ: ripple _____
- [ ] 100 mA load: continuous or discontinuous? _____
- [ ] Losses with ideal switch and diode: efficiency goes from _____ to _____, so the losses are in _____
- [ ] Startup inrush peak current: _____

### 2.2 Simulation B: closed loop (LT8640S)

| Experiment | Simulated | Measured (week 3) | Notes |
| --- | --- | --- | --- |
| Startup time to regulation | | | |
| Startup overshoot | | | |
| Line step 18 → 25.2 V: output deviation | | | |
| Load step 0.5 → 2.5 A: overshoot | | | |
| Load step 0.5 → 2.5 A: recovery time | | | |
| Load step with output capacitance doubled | | | |

### 2.3 Plots

| Plot | File |
| --- | --- |
| Switch node and inductor current, steady state | _add `sim/l1-switchnode.png`_ |
| Output ripple at 18 V | _add `sim/l1-ripple-18v.png`_ |
| Load step response | _add `sim/l1-loadstep.png`_ |

## 3. Bill of materials

| Ref | Part | Manufacturer part number | DigiKey link | Qty | Unit price | In stock |
| --- | --- | --- | --- | ---: | ---: | --- |
| | | | | | | |

## 4. Review notes

| Gate | Date | Reviewer | Result and action items |
| --- | --- | --- | --- |
| Design review (worksheet, simulation, schematic) | | | |
| Layout review | | | |
| Bring-up sign-off | | | |

## 5. Measurement lab

Record in `test/` (scope screenshots, raw data) and summarize here. The **predicted** column comes from your worksheet and the **simulated** column from section 2; where all three disagree, say why in section 6.

| Measurement | Predicted | Simulated | Measured | Notes |
| --- | --- | --- | --- | --- |
| Output voltage at 0 A | 5.00 V | | | |
| Output voltage at 1 A | | | | |
| Output voltage at 2 A | | | | |
| Output voltage at 3 A | | | | |
| Output ripple at 3 A (peak-to-peak) | | | | |
| Duty cycle at 18 V | | | | |
| Duty cycle at 25.2 V | | | | |
| Efficiency at 0.5 A | | | | |
| Efficiency at 1.5 A | | | | |
| Efficiency at 3 A | | | | |
| Load step 0.5 → 2.5 A overshoot | | | | |
| Load step recovery time | | | | |
| Regulator temperature after 10 min at 3 A | | | | |
| Inductor temperature after 10 min at 3 A | | | | |

## 6. What I learned

_Where calculation, simulation, and measurement disagreed, and why._

## 7. Finished board

Photos go in [`photos/`](photos/): bare board, assembled top and bottom, microscope close-ups of the regulator and inductor joints, and the board under test on the bench.

| Assembled board | Under test |
| --- | --- |
| _add `photos/assembled-top.jpg`_ | _add `photos/bench-test.jpg`_ |

<!-- Replace the placeholders with: ![Assembled board](photos/assembled-top.jpg) -->

## 8. Tips for the next person

Written after completing this level, for new members doing it next. Add to each section as you go.

**Worksheet and schematic**
- _tip_

**Simulation**
- _tip_

**Layout**
- _tip_

**Ordering**
- _tip_

**Assembly**
- _tip_

**Bring-up and measurement**
- _tip_

**Mistakes I made and how to avoid them**
- _tip_
