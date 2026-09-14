# Pack Switch Board (1/3-Scale Demonstrator)

**Status:** Preliminary design, not yet drawn in KiCad. The TPS4811-Q1, IPT007N06N, and NTMFS5C604NL were checked against their datasheets; other parts and **all prices are (unv)**: confirm on DigiKey before ordering. Board folder: [`electrical/pcb/pack-switch-board/`](../../electrical/pcb/pack-switch-board/). Design and review rules: [`pcb-design-guide.md`](pcb-design-guide.md).

---

## Job

One board per battery pack, four per demonstrator. It stands in for the full-scale vehicle's **contactor, pre-charge, and fuse**, so the power architecture and its failure modes can be tested at low energy.

| Requirement | Value |
| --- | --- |
| Pack | 6S1P Molicel P50B: 25.2 V full, 21.6 V nominal, ~18 V empty, 60 A continuous |
| Load | Two Zubax Myxa ESCs, input capacitance ~1,000 to 2,000 µF (verify) |
| Current | 55 A continuous (firmware cap), ~14 A hover, ~67 A peaks under 1 s |
| Off state | Blocks current **both directions** so a faulty pack can't be back-fed |
| Turn-on | Pre-charge the ESC capacitance before the main switch closes |
| Enable | Hardwired E-stop loop AND an independent FPGA safety monitor signal; losing either = off; no MCU in the path |
| Protection | Fast hardware overcurrent and short-circuit trip, replaceable fuse, input and output TVS, reverse-pack survival |
| Telemetry | Pack current and voltage to the flight controller |
| Size and mass | ≤ 40 × 60 mm, ~25 to 30 g, no heatsink in flight |
| Assembly | Hand-assembled; no BGA or WLCSP |

---

## Architecture

```
PACK+ ─┬─[FUSE 80 A]─┬─ Q1a‖Q1b ─┬─ Q2a‖Q2b ─[R_sense 0.3 mΩ]─┬─► ESC bus +
       │             │   (common source)  │                     │
  [TVS SMDJ30CA]     └─ R_pre 22 Ω ─ Q3 ──┘                [TVS SMDJ30A]
PACK− ──────────────── GND (star point) ────────────────────────┴─► ESC bus −

TPS48111-Q1 controller
  gate drive → Q1/Q2 (main, back-to-back)    pre-charge gate → Q3
  current sense across R_sense               IMON → flight controller ADC
  fault output → FPGA                        remote temperature diode at the MOSFETs
INA228 on the same shunt (separate Kelvin traces) → I²C → flight controller
Enable: E-stop loop opto AND FPGA ARM opto (in series) → EN/UVLO
        → pre-charge window 0 to 1 s, main switch on after 0.5 s
```

**Why the TI TPS48111-Q1:** it drives back-to-back MOSFETs, has a dedicated pre-charge gate driver, an adjustable 1.2 µs short-circuit trip, and a current monitor output, in a hand-solderable VSSOP-19. TI's own reference design uses two MOSFETs in parallel per side, exactly this layout ([datasheet](https://www.ti.com/lit/ds/symlink/tps4811-q1.pdf), [product page](https://www.ti.com/product/TPS4811-Q1)).

### Controller options considered

| Part | Back-to-back | Pre-charge | Short-circuit trip | Telemetry | Package | Verdict |
| --- | --- | --- | --- | --- | --- | --- |
| **TI TPS48111-Q1** | Yes, 3.7 A / 4 A gate drive | Dedicated driver | Adjustable, 1.2 µs | Current monitor output | VSSOP-19 (E) | **Primary** |
| TI LM74930-Q1 ([TI](https://www.ti.com/product/LM74930-Q1)) | Yes | Gate slew only | Adjustable breaker | Current monitor, 10% | VQFN-24 (M) | **Backup**; set bidirectional mode so ESC regen isn't blocked |
| ADI LTC4368 | Yes | No | Fixed (unv) | No | MSOP-10 (E) | No telemetry, weak drive for 4 MOSFETs |
| TI LM5069 / LM5066 | Single MOSFET | Power limiting | Yes | PMBus (LM5066) | MSOP-10 (E) | Can't block both directions |
| TI LM7480-Q1 | Yes | No | **No** | No | WSON (M) | Rejected |
| Infineon 2ED4820-EM | Yes | Yes | Yes | Yes | TSDSO-24 (M) | Needs SPI setup from an MCU (unv): breaks the no-MCU rule |
| TI TPS1663 / TPS25982 | Integrated | — | — | — | — | Far too small; TPS25982's 24 V limit is below 25.2 V |

---

## Key values

| Function | Value | How |
| --- | --- | --- |
| Bootstrap capacitor | 1 µF | 4 × Qg / 1 V with IPT007N06N (Qg 216 nC) |
| Overcurrent threshold | 80 A | R_IWRN = 11.9 × R_SET / (R_sense × I_OC) = **49.9 kΩ** (R_SET 100 Ω, 0.3 mΩ shunt) |
| Short-circuit trip | 150 A | R_ISCP = I_SC × R_sense / 15.6 µA − 464 = **2.43 kΩ** |
| Overcurrent delay | 5 ms | C_TMR = 82 µA × t / 1.2 = **330 nF**; 67 A peaks never start the timer |
| Fault behavior | **Latch off** | Reset by cycling the E-stop loop, not auto-retry |
| Current monitor | ~3.0 V at 110 A | R_IMON = 9.1 kΩ |
| Pre-charge | 22 Ω into 2,000 µF | τ = 44 ms, 5τ = 220 ms, 1.15 A peak, 0.64 J per start in the resistor |
| Main switch on | 500 ms after enable | Remaining step under 1 mV at 2,000 µF (~0.8 V at 4,000 µF, still far below the trip) |
| Pre-charge window closes | 1 s | So a shorted output can't cook the 22 Ω resistor (29 W if left on) |
| Undervoltage lockout | ~13 V | 470 kΩ / 47 kΩ divider; **not** near 18 V, because ~6 V of sag at 67 A would cut motors in flight |

**Verify ESC capacitance** before finalizing: measure each Myxa input with an LCR meter at 120 Hz, or scope the pre-charge curve and compute C = τ / 22 Ω, and compare with Zubax documentation.

---

## MOSFETs and heat

Rds(on) at 100 °C assumed ≈ 1.6 × the 25 °C maximum (read the exact factor from each datasheet).

| Configuration | Rds(on) max, 25 °C | Path at 100 °C | Loss at 55 A | Per MOSFET | Loss at 67 A |
| --- | --- | --- | --- | --- | --- |
| **2 + 2 Infineon IPT007N06N** (60 V, TOLL, M, RthJC 0.4 K/W; [datasheet](https://www.infineon.com/assets/row/public/documents/24/49/infineon-ipt007n06n-datasheet-en.pdf)) | 0.75 mΩ | 1.20 mΩ | **3.6 W** | 0.9 W | 5.4 W |
| 1 + 1 IPT007N06N | 0.75 mΩ | 2.40 mΩ | 7.3 W | 3.6 W | Too hot |
| 2 + 2 onsemi NTMFS5C604NL (60 V, SO-8FL 5×6, M; [datasheet](https://www.onsemi.com/pdf/datasheet/ntmfs5c604nl-d.pdf)) | 1.2 mΩ | 1.92 mΩ | 5.8 W | 1.45 W | Alternative |
| 2 + 2 Nexperia PSMN1R0-40YLD (40 V, LFPAK56) | 1.1 mΩ | 1.76 mΩ | 5.3 W | — | 40 V too close to the TVS clamp (~48 V) |

**Board total at 55 A:** 3.6 W MOSFETs + 0.9 W shunt + ~0.4 W copper ≈ **5 W**.

**Thermal estimate** (4-layer, 2 oz outer copper, 40 × 60 mm, all estimates):

| Condition | Result |
| --- | --- |
| Hover, 14 A | ~0.24 W in the MOSFETs: negligible |
| 55 A in prop wash | ~12 to 20 K rise |
| 55 A on a still-air bench | ~85 K rise if sustained, time constant ~6 min: **limit static 55 A tests to ~2 min** or add a fan or a 1 mm aluminum spreader (~6 g) |

---

## Enable logic

No MCU, and any lost signal turns the switch off.

- **E-stop loop:** 12 V aux → pull-pin key → normally-closed E-stop → each board's opto LED (TLP293, 2.2 kΩ, ~5 mA, reverse 1N4148 across the LED) → return.
- **FPGA ARM:** 3.3 V FPGA pin → 1 kΩ → a second TLP293 LED, with a 10 kΩ pull-down so it stays off while the FPGA configures.
- **AND:** the two opto transistors are **in series** feeding EN/UVLO. Either LED dark → EN low → gates off within tens of µs.
- **Fault readback:** controller fault pin → 1 kΩ → FPGA (third opto if grounds aren't common).

---

## Protection

| Function | Part | Notes |
| --- | --- | --- |
| Fuse | Littelfuse MAXI 80 A, 32 VDC (e.g. 0299080.ZXNV, unv) in a PCB holder | SMD fuses top out near 40 A (unv). Moving the fuse into the pack lead saves ~8 g |
| Input TVS | SMDJ30CA bidirectional | A reversed pack at 25 V stays below the 30 V standoff |
| Output TVS | SMDJ30A | Catches ESC-side spikes, including regen when the switch opens |
| Reverse pack | Back-to-back MOSFETs block the power path; Schottky in the controller ground return and series resistors on logic pins | Controller input is only rated to about −1 V: **validate before relying on it** (unv) |
| Connectors | **AMASS AS150U** in the harness (~70 A rated, unv) | XT60 (30 A rated) is inadequate; XT90 is marginal |

---

## Bill of materials (per board, prices unv)

| Qty | Part | Package (assembly) | Unit | Ext |
| ---: | --- | --- | ---: | ---: |
| 1 | TI TPS48111QDGXRQ1 | VSSOP-19 (E) | 2.60 | 2.60 |
| 4 | Infineon IPT007N06NATMA1 | TOLL (M) | 6.40 | 25.60 |
| 1 | 0.3 mΩ shunt, Vishay WSLP3921 series (value unv) | 3921 (E) | 2.50 | 2.50 |
| 1 | TI INA228AIDGSR (telemetry, 85 V bus rating) | VSSOP-10 (E) | 4.30 | 4.30 |
| 1 | Infineon IRLML0060TRPBF (pre-charge switch) | SOT-23 (E) | 0.45 | 0.45 |
| 1 | Bourns PWR263S-20-22R0F (pre-charge resistor) | TO-263 (E) | 2.90 | 2.90 |
| 2 | SMDJ30CA + SMDJ30A TVS | SMC (E) | 0.75 | 1.50 |
| 2 | Toshiba TLP293 optocoupler | SO-4 (E) | 0.55 | 1.10 |
| 2 | TI SN74LVC2G17 + SN74LVC1G97 (enable timing) | SOT-23-6 (E) | 0.60 | 1.20 |
| 1 | TI TPS7A4001 5 V LDO | MSOP-8 PowerPAD (M) | 2.20 | 2.20 |
| 1 | MMBT3904 (temperature sense) | SOT-23 (E) | 0.10 | 0.10 |
| 1 | MAXI 80 A fuse and holder | Leaded (E) | 5.00 | 5.00 |
| 1 | AS150U connector pair | Harness | 6.00 | 6.00 |
| — | 0603 passives, 4-layer 2 oz PCB | — | 10.00 | 10.00 |
| | **Total per board** | | | **≈ $65** |

Four boards ≈ $262; five (one spare) ≈ $327. Optional footprint for an STM32G0B1 and TCAN1044 to add CAN telemetry later; firmware stays out of the safety path.

---

## Failure modes

| Failure | Effect | Detection or mitigation |
| --- | --- | --- |
| Input-side MOSFET shorts | Output rises to ~pack − 0.7 V while disabled | FPGA reads an output divider; board is no-fly |
| Output-side MOSFET shorts | Switching works, reverse blocking lost | Only found by back-feeding the output on the bench: part of pre-flight board test |
| Both short / stuck on | E-stop can't cut power; fuse only clears a dead short | Independent ESC-level disarm over CAN; optional independent gate clamp |
| E-stop wire breaks or unplugs | LED dark → off | Safe by design |
| E-stop wires shorted together (bypass) | **Not detected** | FPGA monitors each loop segment |
| Kelvin sense trace opens | Overcurrent protection silently lost | Continuous cross-check of IMON against INA228 |
| Pre-charge resistor opens | Main switch closes into empty capacitors → short-circuit trip → latch off | Safe failure |
| Switch opens during regen | ESC bus overvoltage spike | Output TVS |

---

## Bring-up and qualification

Follows the club checklist in [`avionics-parts.md`](../vehicle/avionics-parts.md#6-hand-assembly-and-qualification), plus:

1. Unpowered: gate-source and drain-source resistance on all four MOSFETs; shunt Kelvin continuity.
2. Bench supply 25 V at 100 mA limit: bootstrap reaches ~12 V; enable truth table (loop only, ARM only, both) works; output 0 V when disabled.
3. Pre-charge into a 2,000 µF bank: scope τ; main switch closes only after the bank is charged.
4. Back-feed 25 V into the output with the board off: under 1 µA both directions.
5. Overcurrent: ramp a DC load to 80 A, confirm the 5 ms trip. Short circuit: discharge a capacitor bank through a shunt, confirm µs-level trip, latch-off, and reset by cycling the E-stop.
6. E-stop to gate-off time on a scope: target under 200 µs.
7. Calibrate IMON and INA228 at 5, 14, 30, and 55 A.
8. Thermal: 55 A for 2 min in still air and 10 min with a fan, thermocouple on the MOSFETs; junction under 110 °C.
9. Reverse-pack test at 25 V through a current-limited supply.
10. Inject a 150 A turn-off with representative harness inductance; check both TVS clamp levels.
