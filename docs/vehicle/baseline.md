# Vehicle Baseline v0

**Status:** Preliminary. Every number here is a first-pass estimate from momentum theory and published component classes, not test data. The baseline is refined by thrust stand testing and trade studies, and frozen at the Preliminary Design Review (PDR). Changes after PDR go through configuration management.

Last updated: 14 September 2026

---

## 1. Governing constraints

Piloted flight is intended to fall under **14 CFR Part 103**.

| Limit | Value | Design consequence |
| --- | --- | --- |
| Empty weight | < 254 lb (115.2 kg) | Batteries are counted in empty weight. This is the binding constraint. |
| Occupants | 1 | Single seat |
| Full-power level speed | < 55 kn | Not a practical limit for a multirotor; enforced in firmware anyway |
| Operations | Daylight, VFR, not over congested areas | Off-campus test site, visual line of sight |

Constitution Article X adds: tolerance of a single propulsion or power failure, a hardwired E-stop, an emergency power disconnect, and a recovery system, all demonstrated before piloted flight.

---

## 2. Top-level requirements

| ID | Requirement | Value |
| --- | --- | --- |
| VEH-01 | Empty weight, including batteries | ≤ 254 lb hard limit, ≤ 229 lb design target (10% margin) |
| VEH-02 | Maximum pilot mass | 95 kg (209 lb), fully equipped |
| VEH-03 | Gross takeoff mass | ≈ 206 kg (454 lb) |
| VEH-04 | Thrust-to-weight, all motors | ≥ 2.0 at gross mass |
| VEH-05 | Thrust-to-weight, any single motor failed | ≥ 1.7 |
| VEH-06 | Thrust-to-weight, any single battery pack failed | ≥ 1.45, controlled landing |
| VEH-07 | Hover endurance | ≥ 5 min to 20% state of charge (target, pending test) |
| VEH-08 | Operating altitude | Low altitude, set by the recovery system's minimum deployment height and the test procedure |
| VEH-09 | Transport | Fits a standard utility trailer with arms installed or folded |

---

## 3. Configuration

**Leading candidate: coaxial octocopter (X8).** Four arms, each carrying an upper and a lower counter-rotating rotor.

Why X8 over a flat quad or hex:
- A single motor failure leaves its coaxial partner on the same arm, so thrust stays roughly symmetric.
- Eight smaller motors are easier to source and test than four very large ones.
- Compact footprint for a given disk area.

Cost: the lower rotor works in the upper rotor's wake, costing roughly 15 to 25% in hover efficiency versus the same disks side by side. The configuration trade study stays open until PDR (alternatives: flat octo, hex, ducted quad).

| Parameter | Baseline |
| --- | --- |
| Rotors | 8 fixed-pitch, 4 coaxial pairs |
| Propeller diameter | 40 in (1.02 m), carbon fiber |
| Adjacent rotor spacing | ≥ 1.1 D center to center (1.12 m) |
| Footprint | ≈ 2.1 m square, ≈ 2.6 m diagonal span |
| Guarding | Ducts or rotor guards, trade study open (ducts add mass) |

---

## 4. Mass budget

| Item | Mass (kg) | Notes |
| --- | ---: | --- |
| Airframe, arms, landing gear | 22.0 | Welded aluminum or steel tube core, composite arms and panels |
| Motors and propellers (8) | 22.0 | ≈ 2.4 kg motor + 0.35 kg prop each |
| ESCs (8) | 5.0 | |
| Battery packs (4) | 42.0 | 24S5P each, cells plus enclosure and BMS |
| HV distribution, contactors, fuses, harness | 6.0 | |
| Flight controller, safety monitor, sensors | 2.0 | |
| Seat, restraint, pilot controls | 4.0 | |
| Ballistic recovery parachute | 8.0 | Sized for gross mass |
| **Total** | **111.0** | **244.7 lb** |
| Part 103 limit | 115.2 | 254 lb |
| **Margin** | **4.2** | **9.3 lb (3.7%)** |

**The baseline does not yet meet the 10% design margin.** Closing about 7 kg (16 lb) is the main design problem for year one. Candidate levers:
- Airframe mass through FEA-driven tube sizing and composite arms
- Higher specific-energy cells, if they still meet the discharge requirement
- Motor and propeller selection from thrust stand data rather than catalog ratings
- Recovery system mass versus effectiveness at low altitude
- Harness routing and bus bar design

---

## 5. Propulsion

Hover estimate from momentum theory at 206 kg gross, total disk area 3.24 m², with a coaxial penalty of 1.25, rotor figure of merit 0.72, and motor plus ESC efficiency 0.88:

| Parameter | Estimate |
| --- | --- |
| Ideal induced hover power | ≈ 32 kW |
| Electrical hover power | ≈ 55 to 65 kW (≈ 63 kW conservative) |
| Hover thrust per rotor | ≈ 25 kgf |
| Peak static thrust per rotor | ≥ 51 kgf (sets VEH-04 to VEH-06) |
| Rotor speed at peak thrust | ≈ 3,700 rpm |
| Tip speed at peak thrust | ≈ 200 m/s (noise and efficiency driver) |
| Electrical power per motor, hover / peak | ≈ 8 kW / ≈ 20 kW |
| Current per motor at ≈ 85 V, hover / peak | ≈ 90 A / ≈ 240 A |

### Motor requirements

| Parameter | Requirement |
| --- | --- |
| Type | Outrunner BLDC, heavy-lift UAV class |
| Rated voltage | 24S (≈ 100 V max) |
| Kv | ≈ 40 to 60 rpm/V, to keep tip speed near 200 m/s |
| Continuous power | ≥ 10 kW |
| Peak power | ≥ 20 kW for 30 s |
| Mass | ≤ 2.5 kg |
| Candidates | T-Motor U15-class outrunners; integrated heavy-lift agricultural drone power systems. Final pick from thrust stand data. |

### ESC requirements

| Parameter | Requirement |
| --- | --- |
| Voltage rating | ≥ 120 V (24S with margin) |
| Current | ≥ 120 A continuous, ≥ 250 A peak |
| Control | Field-oriented control (sinusoidal) |
| Interface | DroneCAN with RPM, current, voltage, and temperature telemetry; PWM fallback |
| Protection | Over-current, over-temperature, and a defined response to loss of command |

---

## 6. Energy storage

| Parameter | Baseline |
| --- | --- |
| Chemistry | Li-ion NMC, 21700 high-power cells (Molicel P45B class: 4.5 Ah, 45 A continuous, ≈ 70 g) |
| Architecture | 4 independent packs, 24S5P each (120 cells, ≈ 10.5 kg) |
| Pack voltage | 86.4 V nominal, 100.8 V full, ≈ 72 V cutoff |
| Energy | ≈ 1.95 kWh per pack, ≈ 7.8 kWh total |
| Usable energy | ≈ 6.2 kWh (to 20% state of charge) |
| Cell current at hover | ≈ 37 A, below the 45 A continuous rating |
| Estimated hover time | ≈ 5 to 6 min to 20% state of charge |

**Pack-to-motor mapping.** Each pack powers two motors on opposite arms that spin in opposite directions. Losing a pack removes two motors without a yaw imbalance and leaves six motors (thrust-to-weight ≈ 1.5). The surviving three packs then run cells near 49 A, above continuous rating, which is acceptable only for an emergency landing.

**Per-pack protection.**
- BMS with per-cell voltage and temperature monitoring
- Main contactor with pre-charge circuit
- Fuse sized to the pack's peak current
- Pack log (cycles, capacity checks, incidents), as Article X, Section 7 requires

**Why Li-ion cells over LiPo pouches.** Better specific energy at this discharge rate, better thermal behavior, and easier to inspect and log per cell. The subscale demonstrators use LiPo.

---

## 7. High-voltage safety

The bus runs above 60 V DC, so it is treated as hazardous voltage.

- A **hardwired E-stop loop** opens every pack contactor directly, with no software in the path.
- The **independent safety monitor** can also open contactors and trigger the recovery system.
- **Insulation monitoring** on the HV bus.
- Orange HV cabling, touch-safe connectors, and lockout procedures for bench work.
- No energized bench work with propellers installed (Article X, Section 6).

---

## 8. Avionics and computing

This follows Article X, Section 10.

| Function | Baseline |
| --- | --- |
| Flight controller | STM32H7-class MCU running a real-time OS (ArduPilot on ChibiOS or PX4 on NuttX), no general-purpose OS in the loop |
| Sensors | Triple-redundant IMU, dual barometer, GNSS, downward LiDAR rangefinder |
| Motor bus | DroneCAN to all 8 ESCs |
| Independent safety monitor | Small FPGA or CPLD (Lattice iCE40 class) on its own power rail and reset domain. Watches the flight controller heartbeat and attitude, rate, and power limits. Has authority to cut motor power and fire the recovery system. |
| Companion computer | FPGA SoC (Zynq class). Logging, telemetry, and perception, advisory only, with no path to motor command. |
| Low-voltage power | Redundant isolated DC-DC converters from the HV bus to 12 V and 5 V rails |
| Pilot interface | Fly-by-wire, attitude-stabilized. The pilot commands and the controller stabilizes. |
| Ground station | Telemetry link to ground station with a range safety officer E-stop |

---

## 9. Subscale demonstrator (Phase 1)

About one-third geometric scale, flying the same architecture at low energy so the software, the safety monitor, and test procedures mature first.

| Parameter | Target |
| --- | --- |
| Configuration | Coaxial X8 |
| Propellers | 15 in |
| Battery | 6S LiPo (22.2 V nominal) |
| All-up mass | ≈ 5 kg |
| Flight controller | Same family as the full-scale vehicle |
| Safety monitor | Same RTL as full scale, on a dev board |
| Low-voltage power | Team-built buck converter board (6S to 5 V) |

---

## 10. Open trade studies

1. Configuration: coaxial X8 versus flat octo versus ducted quad
2. Airframe material: welded aluminum versus steel tube versus composite monocoque
3. Cell selection: discharge capability versus specific energy
4. Recovery system: ballistic parachute mass versus minimum deployment height
5. Ducts versus open rotors with guards
6. Pilot mass cap versus endurance

## Method notes

- Ideal hover power: P = T^1.5 / sqrt(2 ρ A), with ρ = 1.225 kg/m³ and A the total disk area of four rotor positions.
- Rotor thrust and power: T = C_T ρ n² D⁴ and P = C_P ρ n³ D⁵, with C_T ≈ 0.10 and C_P ≈ 0.055 for a static fixed-pitch prop. Replace these with thrust stand data as soon as it exists.
- Energy: cells × 4.5 Ah × 3.6 V.
