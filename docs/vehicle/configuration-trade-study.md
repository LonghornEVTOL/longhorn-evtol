# Configuration Trade Study

**Status:** Open. Baseline is the coaxial X8 (Option A). This document keeps the credible alternatives sized on the same assumptions, so the team can pivot quickly if tests or requirements change. The decision is made at the Preliminary Design Review (PDR).

Last updated: 14 September 2026 · Companion to [`baseline.md`](baseline.md)

---

## Terms

| Term | Meaning |
| --- | --- |
| **Coaxial** | Two motors stacked on one arm, one above and one below, spinning in opposite directions. Both push air down. The lower rotor works in the upper rotor's wake and loses about 15% thrust. |
| **Flat** | One motor per arm, all rotors in one plane with clean air. More efficient, but needs more arms and a bigger footprint for the same prop size. |
| **X8** | 4 arms × 2 coaxial rotors = 8 rotors |
| **X12** | 6 arms × 2 coaxial rotors = 12 rotors |
| **Octo / hexa** | 8 or 6 arms, one rotor each |

---

## Common assumptions

Every option carries the same design point and fixed equipment so the comparison is fair.

- Pilot 160 lb (72.6 kg). Requirement: total thrust ≥ 2 × gross weight.
- Part 103 empty weight limit 254 lb (115.2 kg), batteries included.
- Fixed items in every option: landing gear 3.0 kg, HV distribution 1.0 kg, avionics and LV power 4.0 kg, seat and controls 7.3 kg, ballistic parachute 9.5 kg.
- Batteries: 4 × 18S5P Molicel P50B packs, 38.6 kg and 6.5 kWh, unless stated.
- Coaxial losses: 0.85 × thrust per rotor, +18% hover power.
- Adjacent rotors spaced at least 1.1 × prop diameter.
- Hover power from manufacturer thrust-per-watt data; endurance is energy to 20% charge, before thermal limits.
- **Airframe and mount masses for B through D are engineering estimates** scaled from the baseline, not designs.

---

## Summary

| | **A. Coaxial X8** (baseline) | **B. Flat octo** | **C. Coaxial X12** | **D. Flat hexa** |
| --- | --- | --- | --- | --- |
| Layout | 4 arms × 2 rotors | 8 arms × 1 rotor | 6 arms × 2 rotors | 6 arms × 1 rotor |
| Propulsion | 8 × Hobbywing X13 G2, 56 in | 8 × X13 G2, 56 in | 12 × Hobbywing X11 Max, 48 in | 6 × X13 G2, 56 in |
| Overall span | **3.6 m** | 5.5 m | 3.9 m | 4.6 m |
| Empty weight, parachute counted | 250 lb | 250 lb (smaller packs) | 254 lb | **234 lb** |
| Empty weight, parachute excluded | 229 lb | 229 lb | 233 lb | **213 lb** |
| Total thrust | 408 kgf | **480 kgf** | 449 kgf | 360 kgf |
| Thrust-to-weight | 2.19 | **2.58** | 2.39 | 2.02 |
| After one motor fails | 1.92 | **2.26** | 2.19 | 1.68, and see controllability risk |
| After one battery pack fails | 1.65 | **1.94** | 1.79 | 1.34 |
| Hover power | 25.5 kW | **21.6 kW** | 29.2 kW | 20.8 kW |
| Endurance (energy) | 12 min | 12 min | 11 min | 15 min |
| Rotor load at hover | 27 kgf effective | **23 kgf** | 18 kgf | 30 kgf, above the unit's 25 to 27 kgf sweet spot |
| Arms and folding joints | **4** | 8 | 6 | 6 |
| Main risk | Thin weight margin; lower rotors lose efficiency | Size and transport | Zero weight margin; highest hover power | Loss of control after a motor failure |

---

## Option A: Coaxial X8 (current baseline)

**Why it's the baseline:** most compact layout for 56 in props, fewest arms, and each arm keeps a working rotor if its partner fails. Jetson ONE flies this layout under Part 103.

| Pros | Cons |
| --- | --- |
| Smallest footprint (3.6 m), easiest transport | Lower rotors lose about 15% thrust; highest hover power of the 56 in options |
| 4 arms and 4 fold joints: least structure | Only 4 lb of margin to 254 lb with the parachute counted |
| Motor failure leaves thrust on every arm | Upside-down lower motors, stacked mounts, and rotor spacing to tune |

**Pivot away if:** the coaxial loss measured on the thrust stand is well above 15%, or the mass budget grows past the limit with no other place to cut.

---

## Option B: Flat octocopter (8 arms)

Same motors as the baseline, each on its own arm.

| Pros | Cons |
| --- | --- |
| Every rotor in clean air: most thrust (480 kgf) and least hover power | **5.5 m across.** Nearly twice the baseline's width, more than twice trailer width. Needs 8 folding arms. |
| Best failure tolerance: 2.26 after a motor out, 1.94 after a pack out | Longer arms add about 6 kg (estimate). With the same batteries the vehicle is 262 lb, **over the limit**. |
| Simple identical arms; standard heavy-lift drone layout; no coaxial tuning | Stays legal only by shrinking the packs about 15%, which the efficiency gain makes possible |
| Rotors run nearer their efficient hover point | 8 hinges to design, qualify, and inspect before every flight |

**Weight note.** Moving to 8 arms adds structure but cuts hover power by 15%. Resizing the packs to 85% (about 33 kg) keeps the same 12 min endurance and brings empty weight back to 250 lb. Weight is a wash, and the real cost is size.

**Pivot to B if:**
- the coaxial thrust stand test shows losses much larger than 15%, or
- the team moves to smaller props (see note below), or
- a transport and folding-arm design proves workable.

**With smaller props:** at 40 to 48 in, the flat octo footprint drops to about 4 to 4.7 m. Per-rotor thrust also drops (Hobbywing X11 Max: 44 kgf), and 8 × 44 = 352 kgf misses the 372 kgf target. A flat octo with smaller props needs either a lighter vehicle or higher-thrust units.

---

## Option C: Coaxial X12 (6 arms × 2 rotors)

Twelve smaller [Hobbywing X11 Max](https://www.hobbywing.com/en/uploads/file/20260707/743b1d5ca79ea1a674b581acae39a30a.pdf) units (2.8 kg, 44 kgf max, 48 in props).

| Pros | Cons |
| --- | --- |
| **12 motors:** losing one costs only 8% of thrust | Empty weight lands **exactly at 254 lb** with the parachute. No margin at all. |
| Each unit runs at about 18 kgf, below its 20 to 22 kgf rating, so the ESCs stay cool during a motor-out landing | Highest hover power (29 kW) because the rotors are smaller, and shortest endurance |
| Compact (3.9 m), only slightly larger than A | 12 motors and ESCs to buy, wire, and maintain; 6 arms |
| Lower-thrust units are cheaper to test on a smaller thrust stand | Pack mapping gets more complex (3 motors per pack) |

**Pivot to C if the motor-out thermal test on the X13 G2 fails** (baseline open item 1). C spreads the load across more, lighter-loaded units, which directly fixes that failure mode. It only works if the parachute exclusion is confirmed or mass comes out elsewhere.

---

## Option D: Flat hexacopter (6 arms)

Six of the baseline motors, one per arm.

| Pros | Cons |
| --- | --- |
| **Lightest option:** 234 lb, 20 lb of margin | **Controllability after a motor failure.** Published analyses show a standard hexacopter with alternating spin directions cannot keep full control of all axes after losing one rotor. Some alternative spin patterns improve this, but it has to be proven. |
| Least hover power and longest endurance | Thrust-to-weight only 2.02 with all motors, and 1.34 after a pack failure |
| Fewest motors to buy | Each unit hovers near 30 kgf, above its sweet spot, which means more heat and less margin |
| | 4.6 m across |

**Status: not recommended for a piloted vehicle.** Article X, Section 5 requires demonstrated motor-out response, and the hexacopter makes that the hardest to show. It is kept here because of its weight margin: it becomes worth reconsidering only if the weight limit becomes the problem everything else can't solve.

---

## Rejected outright

| Option | Why |
| --- | --- |
| **Quadcopter, including ducted** | Losing any one motor loses the vehicle. It can't meet Article X's motor-out requirement without a separate backup lift system. |
| **Coaxial hexa with small props** (Y6-style, 3 arms × 2) | Three arms make thrust after a failure very lopsided; no weight or footprint advantage over A |

---

## How the decision gets made

Tests that settle this, roughly in order:

1. **Coaxial loss.** Run one X13 G2 alone, then stacked in a coaxial pair on the thrust stand. If the pair makes much less than 1.7 × one rotor's thrust, B gains ground.
2. **Motor-out thermal.** Hold one X13 G2 at 40 to 45 kgf for 120 s. Failing this moves the baseline toward C.
3. **Parachute exclusion.** FAA's reading decides whether A and C have real margin.
4. **Folding arm design.** Mass and stiffness of a real hinge sets the arm penalty for B, C, and D.
5. **Subscale comparison.** The 1/3 scale demonstrator can be built as both a coaxial X8 and a flat octo to compare efficiency, handling, and motor-out behavior cheaply.

The Systems Engineering team owns this document. Any change to the baseline configuration goes through configuration management and is re-reviewed at PDR.

## Method notes

- Span: rotor positions placed on a circle with adjacent spacing 1.1 D; span = 2 × circle radius + D. For A (4 positions) this equals the diagonal span.
- Endurance: 6.5 kWh × pack scale × 80% usable ÷ hover power.
- One motor out: (n − 1) × rotor thrust × coaxial factor ÷ gross weight. One pack out removes 2 motors (3 for C).
- Airframe estimates: B adds about 6 kg for 8 longer folding arms; C adds about 0.7 kg for 6 short arms plus 1 kg of stacked mounts; D adds about 1.7 kg for 6 longer arms. All replaced by CAD and FEA mass once designs exist.
