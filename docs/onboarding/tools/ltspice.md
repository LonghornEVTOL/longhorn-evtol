# LTspice

Every board in the ladder gets simulated before it gets a schematic. A simulation costs ten minutes; a fab run costs two weeks and real money, and a wrong inductor value is invisible until the board is on the bench. Simulation also shows you things you cannot probe later: inductor current, the instant power is applied, and what happens in a fault you would not want to create on purpose.

LTspice is free, needs no license or account, and is what the club uses for circuit-level work. KiCad has a built-in simulator (ngspice); we use LTspice because its models and its convergence behaviour are better for switching converters.

---

## Install

**Windows** (what most lab machines run)

1. Go to `analog.com/en/resources/design-tools-and-calculators/ltspice-simulator.html`, or search "Analog Devices LTspice" if that page moves.
2. Download the Windows installer and run it. Defaults are fine; no account needed.
3. Open LTspice and run **Tools → Sync Release**. This downloads the current symbol and model library. It takes a few minutes, you only do it once, and it is not optional: without it, many Analog Devices parts will not simulate.
4. **Tools → Control Panel → Operation**, tick *Automatically delete .raw files*. Those files reach hundreds of megabytes and must never end up in Git.

**macOS**

Same page, download the macOS build, drag it to Applications, then **Tools → Sync Release**. The Mac menus and shortcuts differ a little from the Windows ones (`Cmd` instead of `Ctrl`); everything else in this guide applies.

**Linux**

No native build. Run the Windows version under Wine, or use a lab machine.

---

## The ten things you need to know

| Action | How |
| --- | --- |
| Place a part | `F2`, then type the name (`res`, `cap`, `ind`, `diode`, `sw`, `LT8640S`) |
| Fast placement | `R` resistor, `C` capacitor, `L` inductor, `D` diode, `G` ground |
| Wire | `F3`, click point to point, `Esc` to stop |
| Name a net | `F4`, type the name (`in`, `sw`, `out`), drop it on the wire |
| Rotate / mirror while placing | `Ctrl+R` / `Ctrl+E` |
| Move / drag / delete / copy | `F7` / `F8` / `F5` / `F6` |
| Set a value | Right-click the part. For an inductor or capacitor, also fill in `Series Resistance` |
| Add a SPICE directive | `S`, type the line, place it on the sheet |
| Run | the running-man button, or `Alt+R` |
| Probe | click a wire for voltage, click a part body for current |

In the waveform window: drag across the x-axis to zoom, `Ctrl+left-click` a trace name for its **average and RMS** over the visible window, right-click a trace name to edit the expression. The circuit needs a ground symbol somewhere or nothing runs.

**Where the numbers come out:** `.meas` statements print to the SPICE error log, `Ctrl+L`. Read them there instead of eyeballing the plot.

---

## Level 1: the buck converter simulations

You build the converter twice: once out of ideal pieces so you can see the mechanism, and once with a real regulator chip so you can see what a control loop does. Do the worksheet first. The point is to compare hand calculation against simulation against, three weeks later, the measured board.

### Simulation A: open-loop buck, built from parts

Draw this yourself. Do not download a finished buck converter; drawing it is most of the lesson.

| Part | Value |
| --- | --- |
| `V1`, `in` to ground | `{Vin}` |
| `V2`, switch control pin to ground | `PULSE(0 5 0 1n 1n {D/fsw} {1/fsw})` |
| `S1` voltage-controlled switch (`sw`), `in` to the `sw` node | model `MYSW`, below |
| `D1`, ground to the `sw` node, cathode on `sw` | `MBRS340` or any Schottky |
| `L1`, `sw` to `out` | your calculated value; set `Series Resistance` to the DCR from the datasheet |
| `C1`, `out` to ground | your calculated value; set `Series Resistance` to the ESR from the datasheet |
| `Rload`, `out` to ground | 1.67 (5 V at 3 A) |

Directives, each placed with `S`:

```
.param Vin=18  fsw=400k  D=0.28
.model MYSW SW(Ron=25m Roff=1Meg Vt=2.5 Vh=-0.1)
.tran 0 2m 1.5m 10n
.meas TRAN Vout    AVG V(out)            FROM 1.8m TO 2m
.meas TRAN Vripple PP  V(out)            FROM 1.8m TO 2m
.meas TRAN ILavg   AVG I(L1)             FROM 1.8m TO 2m
.meas TRAN ILpp    PP  I(L1)             FROM 1.8m TO 2m
.meas TRAN Pin     AVG (-1)*V(in)*I(V1)  FROM 1.8m TO 2m
.meas TRAN Pout    AVG V(out)*I(Rload)   FROM 1.8m TO 2m
.meas TRAN Eff     PARAM Pout/Pin
```

`.tran 0 2m 1.5m 10n` means: run to 2 ms, keep only the data after 1.5 ms so you measure steady state instead of startup, and never take a time step longer than 10 ns. Replace `fsw`, `D`, `L1`, and `C1` with your own numbers.

`I(V1)` is negative while the source delivers power, which is why `Pin` carries the `-1`. Sign conventions like that are normal SPICE, not a mistake in your circuit.

Run these experiments and record every one in your README:

| # | Experiment | What to compare | What it teaches |
| --- | --- | --- | --- |
| 1 | Set `D` to your calculated duty cycle and measure `Vout` | 5.00 V, or low? | The ideal `D = Vout/Vin` ignores the diode drop, switch `Ron`, and inductor DCR. Real converters sit at a higher duty cycle |
| 2 | `.step param Vin list 18 21 25.2`, and find the `D` that gives 5.00 V at each | Duty cycle vs input voltage | Why a feedback loop has to exist at all |
| 3 | Compare `ILpp` with your ripple-current calculation | Should agree within a few percent | Whether your inductor equation is right |
| 4 | Halve `L1` and rerun | Ripple current doubles | Ripple is set by L, and what "20 to 40 percent ripple" buys you |
| 5 | Compare `Vripple` with your capacitor calculation, then set the capacitor's `Series Resistance` to 50 m and rerun | Ripple jumps and changes shape | Above a few hundred kHz, ESR usually sets the ripple, not capacitance. This is why the BOM says ceramic |
| 6 | Set `Rload` to 50 (a 100 mA load) and plot `I(L1)` | Inductor current hits zero and sits flat | Continuous vs discontinuous conduction, and why the output voltage moves at light load |
| 7 | Record `Eff`, then set `Ron=1m` and use an ideal diode, and rerun | Where the efficiency went | The conduction-loss budget, and why a synchronous FET beats a diode |
| 8 | Delete the `1.5m` save-start from `.tran` and look at the first 200 us | Inrush current into the output capacitor | Why soft-start exists, and why hot-plugging a pack is hard on a board |

### Simulation B: closed loop, with a real regulator

The TI LMR33630 has no LTspice model: TI ships encrypted PSpice and TINA models that LTspice cannot read. Simulate the closest Analog Devices part instead, the `LT8640S` (42 V in, 5 A synchronous buck). The topology, the feedback divider, and the loop behaviour teach the same lesson.

1. `F2`, type `LT8640S`, place it. Right-click the symbol and choose **Open this macromodel's test fixture** for a working circuit to start from.
2. Set the input to your range and size the feedback divider for 5.0 V using *this part's* reference voltage from *its* datasheet, not the TI one.
3. Run and record:

| Experiment | Measure | Compare against |
| --- | --- | --- |
| Startup from 18 V into a 3 A load | Time to regulation, overshoot | The bench, in week 3 |
| Line step 18 to 25.2 V (`PWL` source) | Output deviation and recovery time | The datasheet's line transient plot |
| Load step 0.5 to 2.5 A (`PULSE` on a current-source load) | Overshoot in mV, recovery in us | Your own bench load-step measurement |
| The same load step with the output capacitance doubled | Overshoot drops, recovery slows | What output capacitance actually does for the loop |

Write the numbers down. In week 3 you put the real board on a scope and fill in the third column of the same table; that comparison is the deliverable.

---

## Level 2 simulations

Every required feature gets simulated before it goes into the schematic, and the simulation comes with you to the concept review.

**Option A, protected power module**

- Reverse-polarity P-FET: sweep the input from -25 V to +25 V with `.dc`, plot the FET current to show nothing flows backwards, and check that the gate-source voltage never exceeds its rating.
- UVLO with hysteresis: ramp the input up and back down with a `PWL` source, plot the enable pin and the output, and read both thresholds off the plot. They have to match your divider math.
- Comparator and latch: trip it with a current step and show it stays latched until the input is cycled.
- RC filter into the ADC: `.ac dec 100 1 1Meg`, read the -3 dB point off the Bode plot, and compare with `f = 1/(2*pi*R*C)`.
- TVS and hot-plug: step the input through a few hundred nH of wiring inductance with a `PWL` source, and check the input clamps below the regulator's absolute maximum rating.

**Option B, strobe driver**

- Current loop: step the set point, plot LED current, measure overshoot and settling time.
- Open-LED fault: switch the LED string out mid-run and show where the output voltage goes and what clamps it.
- Gate drive: plot the gate waveform with and without a gate resistor, and look at the switching edge.

---

## What to commit

Everything lives in the `sim/` folder in your workspace.

| File | Commit it? |
| --- | --- |
| `.asc` schematic | Yes, always. This is the actual work |
| `.plt` plot settings | Yes, so your reviewer opens the same plot you did |
| `.png` screenshots of each plot | Yes, and reference them from your README |
| `.raw`, `.log`, `.net`, `.op.raw` | No. Already ignored in `.gitignore`, and they are huge |

To save a plot: click the waveform window, then **Tools → Copy bitmap to Clipboard**, paste into any image editor, and save as PNG. The Windows Snipping Tool works just as well. Name the files for what they show: `sim/l1-ripple-18v.png`, not `sim/plot3.png`.

---

## When LTspice complains

| Message or symptom | Cause | Fix |
| --- | --- | --- |
| `Analysis: Time step too small` | Convergence, usually from perfectly ideal parts | Give parts real values: switch `Ron` above zero, series resistance on the inductor and capacitor, a small resistance in series with ideal sources |
| `Unknown subcircuit called in ...` | Model library out of date | **Tools → Sync Release**, then restart |
| The simulation runs forever | Stop time is enormous compared with the switching period | Simulate milliseconds, not seconds, and always set a maximum time step |
| The output is zero or a flat line | No ground symbol, or a net-name typo left a node floating | Place `G` ground, check names with `F4` |
| `.meas` results are nowhere | They print to the log, not the plot | `Ctrl+L` |
| Traces look like triangles with no detail | Maximum time step too large | Last argument of `.tran`; use about 1/200 of the switching period |
| You want to simulate the exact TI part | TI ships PSpice and TINA models, not LTspice ones | Simulate the topology, use the LT8640S for the closed-loop check, and cross-check the TI part in TI WEBENCH |

---

## Resources

- LTspice tutorials on the Analog Devices site, in the same place as the download
- The regulator datasheet: its typical application circuit is the answer key for your schematic
- TI WEBENCH for a second opinion on component values (browser-based, free with a TI account)
- _Club lessons and videos: to be added_
