# Avionics and Perception Parts

**Status:** Proposed parts for funding and design. Every custom board here is designed and **hand-assembled by the club** from individual chips. Prices are DigiKey or manufacturer quantity-1 prices checked 14 September 2026 unless noted. **(unv)** marks anything not confirmed on a manufacturer or distributor page. Recheck stock before ordering; several parts below are out of stock or end-of-life.

Companion to [`subsystems.md`](subsystems.md) (what boards exist) and [`baseline.md`](baseline.md) (vehicle sizing).

---

## Assembly difficulty

| Rating | Packages | What it takes |
| --- | --- | --- |
| **E** Easy | SOIC, TSSOP, SSOP, LQFP/TQFP, 0603 passives, modules with castellated edges | Stencil and hot plate, or an iron for leaded parts |
| **M** Moderate | QFN and LGA with hidden or exposed pads, 0402 passives | Stencil, paste, hot plate or reflow oven; verify by reading chip ID registers because the joints can't be seen |
| **H** Hard | BGA, WLCSP, 0.4 mm pitch QFN | Needs X-ray inspection. **Avoided** in every pick below |

Design rules for club boards: no BGA or WLCSP, 0603 passives by default (0402 only where needed), LQFP over QFN when the same chip offers both. Boards are designed in **KiCad**; library, layout, review, and release rules are in [`../hardware/pcb-design-guide.md`](../hardware/pcb-design-guide.md).

---

## Summary

| Board or system | Primary chips | Parts per unit | Notes |
| --- | --- | --- | --- |
| [Flight controller](#1-flight-controller) | STM32H753IIT6, ICM-45686 + BMI088 + IIM-42653, BMP390 + MS5611, MMC5983MA | **≈ $145** | Pixhawk FMUv6X-compatible |
| [GNSS and compass node](#21-gnss-and-compass-node) | u-blox ZED-F9P-04B, IIS2MDC (RM3100 for full scale) | **≈ $218** | |
| [Barometer node](#22-barometer-node) | 2 × Bosch BMP581 | **≈ $27** | |
| [Optical flow node](#23-optical-flow-and-height-node) | PixArt PAA3905E1-Q, Broadcom AFBR-S50LV85D | **≈ $114** | Flow sensor supply is poor |
| [Power monitor node](#25-power-monitor-node) | TI INA228 + 100 µΩ shunt | **≈ $48** | |
| [Vibration logger](#24-imu-vibration-logger-test-tool) | ICM-42688-P, optional BNO085 | **≈ $89** | Test tool only |
| [Safety monitor](#3-safety-monitor-board) | Microchip IGLOO2 M2GL010-TQG144I, Murata SCH16T | **≈ $272** | |
| [Pilot controls and cockpit](#4-pilot-controls-and-cockpit) | APEM HF grips, 2 × STM32G474, 2 × ADS131M04, Newhaven 7 in EVE display | **≈ $854** | |
| [Perception, subscale](#5-perception-and-companion-computer) | Jetson Orin Nano Super, 2 × OV9281, OAK-D Pro W, SF45/B, SIYI HM30 | **≈ $2,682** | |
| [Perception, full scale](#5-perception-and-companion-computer) | Jetson Orin NX 16GB on club carrier, OAK-D Pro W ×3, Livox Mid-360, Herelink | **≈ $10,330** | |

See [funding summary](#8-funding-summary) for totals including spares, PCB fabrication, and assembly tools.

---

## Changes to the baseline from this research

| Baseline v1 said | Now proposed | Why |
| --- | --- | --- |
| Companion: AMD Kria K26 | **NVIDIA Jetson Orin NX 16GB** on a club carrier; Kria KV260 kit kept for FPGA image experiments | Orin plugs into a SO-DIMM socket whose leads can be reflowed and inspected by hand; the K26 uses Samtec connectors with hidden joints that need X-ray. Orin also has the strongest ROS 2 and VIO support. |
| Safety monitor FPGA: Lattice MachXO3D | **Microchip IGLOO2 M2GL010-TQG144I** (backup Lattice MachXO2 LCMXO2-7000HC in TQFP-144) | MachXO3D is BGA or QFN-72 only; IGLOO2 comes in LQFP-144, and its flash configuration is immune to radiation upsets |
| Flight controller barometer: TDK ICP-20100 (as on Pixhawk 6X) | **Bosch BMP390 + TE MS5611** | ICP-20100 and ICP-10111 are end-of-life |
| Flight controller: COTS Pixhawk 6X Pro only | COTS stays on the piloted vehicle; **club flight controller** built to the same FMUv6X pinout | Existing PX4 and ArduPilot board configurations work unchanged |

---

## 1. Flight controller

Target: a hand-assemblable board that matches the **Pixhawk FMUv6X** open standard ([Pixhawk standards](https://github.com/pixhawk/Pixhawk-Standards)), so PX4 and ArduPilot board support already exists. References: [Holybro Pixhawk 6X](https://docs.px4.io/main/en/flight_controller/pixhawk6x.html) and [ARK ARKV6X](https://docs.px4.io/main/en/flight_controller/ark_v6x.html) ([ARK docs](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/arkv6x)).

### Main processor

| Part | Specs | Package | Assy | $ |
| --- | --- | --- | --- | --- |
| **STM32H753IIT6** (primary) | Cortex-M7 480 MHz, 2 MB flash, 1 MB RAM, crypto, 2 CAN FD (unv) | LQFP-176, 24 × 24 mm, 0.5 mm | E/M | 20.13 |
| STM32H743ZIT6 (backup) | Same core, no crypto; needs a pin remap | LQFP-144, 0.5 mm | E/M | 18.29 |
| STM32H757IIT6 | Dual core M7 + M4; second core unused by autopilots | LQFP-176, 0.5 mm | E/M | 22.22 |
| **STM32F100C8T6B** (IO coprocessor) | Cortex-M3 24 MHz, 64 KB flash; same as Pixhawk 6X | LQFP-48, 0.5 mm | E | 5.39 |

The ARKV6X uses the BGA H743IIK6; the LQFP H753 is the hand-friendly equivalent. ST has no separate safety-rated H7; functional safety support is a software and documentation package (unv).

### IMUs: three sensors, at least two manufacturers

| Part | Gyro / accel noise | Range | Max rate | Temp | Package | Assy | $ | PX4 / ArduPilot |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **TDK ICM-45686** (IMU 1) | 3.8 mdps/√Hz, 70 µg/√Hz | ±4000 dps, ±32 g | 6.4 kHz (unv) | −40 to 85 °C (unv) | 14-LGA 2.5 × 3 mm | M | 5.26, out of stock | Y / Y |
| **Bosch BMI088** (IMU 2) | 14 mdps/√Hz, 175 µg/√Hz | ±2000 dps, ±24 g | 2 kHz | −40 to 85 °C | 16-LGA 4.5 × 3 mm | M | 5.92 | Y / Y |
| **TDK IIM-42653** (IMU 3) | per datasheet DS-000529 | ±4000 dps, ±32 g | — | −40 to 105 °C | 14-LGA 2.5 × 3 mm | M | 5.96 | Y / Y |
| TDK ICM-42688-P (backup) | 2.8 mdps, 70 µg | ±2000 dps, ±16 g | 32 kHz (unv) | −40 to 85 °C (unv) | 14-LGA 2.5 × 3 mm | M | 4.91 | Y / Y |
| TDK IIM-42652 | 2.8 mdps, 70 µg (unv) | ±2000 dps, ±16 g | 16 kHz | −40 to 105 °C | 14-LGA | M | 4.80, out of stock to Aug 2027 | Y / Y |
| Murata SCH16T-K01 (upgrade) | ~0.3 mdps/√Hz, 0.5 °/h bias instability | **±300 dps**, ±8 g (±26 g max) | up to 11.8 kHz (unv) | −40 to 110 °C | SOIC-24, leaded | **E** | 63.21 | PX4 only |
| ADI ADIS16470 | 8 °/h bias (unv) | ±2000 dps, ±40 g | 2 kHz | unv | Module, 1 mm connector (unv) | E | 547.56, out of stock | Y / Y |
| ADI ADIS16505-2 / 16507-2 | ~3 °/h (unv) | ±500 dps | 2 kHz (unv) | unv | 100-ball BGA | **H**, avoid | 1,046.59 | varies |

The SCH16T gives a true third manufacturer, checksummed SPI, AEC-Q100 automotive grade, and leads you can inspect, but its ±300 dps gyro range and PX4-only driver make it an upgrade rather than the default.

**Why not the CEVA BNO085/BNO086.** It runs its own fusion and outputs orientation at up to 400 Hz; raw gyro tops out at 400 Hz and accel at 500 Hz (flight controllers sample at 1 to 8 kHz); typical latency is 3.7 to 6.6 ms; the accelerometer is fixed at ±8 g and clips under motor vibration; and neither PX4 nor ArduPilot has a driver ([CEVA datasheet](https://www.ceva-ip.com/wp-content/uploads/BNO080_085-Datasheet.pdf)). It is fine for the vibration logger test tool.

### Barometers: two, different manufacturers

| Part | Relative accuracy / noise | Max rate | Package | Assy | $ | PX4 / ArduPilot |
| --- | --- | --- | --- | --- | --- | --- |
| **Bosch BMP390** | ±3 Pa (±25 cm), 0.02 Pa RMS, ±0.6 Pa/K | 200 Hz | 10-LGA 2 × 2 mm | M | 3.30 | Y / Y |
| **TE MS5611-01BA03** | 10 cm resolution (unv) | ~100 Hz (unv) | 8-pad 5 × 3 mm | E/M | 11.56 | Y / Y |
| Bosch BMP581 (backup) | ±6 Pa, 0.08 Pa | 480 Hz | 10-LGA 2 × 2 mm | M | 3.07 | Y / Y |
| Infineon DPS368 | ±2 cm precision (unv) | 200 Hz (unv) | 8-LGA 2 × 2.5 mm | M | 2.70, out of stock | PX4 (unv) |
| TDK ICP-20100 / ICP-10111 | — | — | LGA | M | **End of life** | — |

Cover both with open-cell foam, away from prop wash. Plan downwash tests on the subscale drone; community reports on foam are mixed ([ArduPilot forum](https://discuss.ardupilot.org/t/best-type-of-foam-for-barometer/76293)).

### Magnetometer

| Part | Range / noise | Package | Assy | $ |
| --- | --- | --- | --- | --- |
| **Memsic MMC5983MA** | ±8 G, 0.4 mG RMS (unv) | 16-LGA 3 × 3 mm | M | 3.32 |
| Isentek IST8310 (backup) | ±1600 µT, 0.3 µT (unv) | 16-LGA 3 × 3 mm | M | 2.88 |

On the full-scale vehicle, heading comes from the mast-mounted GNSS node's compass, far from high-current wiring. The onboard magnetometer is secondary.

### CAN FD, memory, power, protection

| Function | Part | Package | Assy | $ |
| --- | --- | --- | --- | --- |
| CAN FD transceiver (×2) | **TI TCAN1462DRQ1**, 8 Mbps, AEC-Q100 (backup NXP TJA1462AT) | SOIC-8 | E | 1.49 |
| Parameter storage | **Infineon FM25V02A-G** FRAM, 256 kbit SPI | SOIC-8 | E | 8.75 |
| Log storage | **JAE ST11S008V4HR2000** microSD socket (the Molex part on older designs is obsolete) | SMD | E | 2.61 |
| Dual power input selection | **TI TPS2121RUXR**, 2.8 to 22 V, 4.5 A (backup ADI LTC4417IGN, SSOP-24, E, $12.50) | VQFN-HR 2 × 2.5 mm | M | 2.44 |
| eFuse (×2) | **TI TPS259470ARPWR**, 2.7 to 23 V, 5.5 A | VQFN-HR 2 × 2 mm | M | 1.82 |
| 3.3 V digital | TI TLV75733PDBVR | SOT-23-5 | E | 0.34 |
| 3.3 V sensors (×2) | TI TPS7A2033PDBVR low-noise LDO | SOT-23-5 | E | 0.35 |
| CAN ESD (×2) | Nexperia PESD2CANFD24V-TR | SOT-23 | E | 0.47 |
| USB/serial ESD (×4) | TI TPD4E05U06DQAR | USON-10 | M | 0.78 |
| IMU heater | PCB-trace or resistor heater (~1 W, as on ARKV6X) on an isolated IMU island, MOSFET on an MCU PWM pin | generic | E | ~2 |

eMMC is not recommended: BGA-153 only, and not used by either autopilot's storage stack (unv).

### Flight controller BOM (one board)

| Function | Part | Qty | Unit $ | Ext $ |
| --- | --- | ---: | ---: | ---: |
| FMU | STM32H753IIT6 | 1 | 20.13 | 20.13 |
| IO | STM32F100C8T6B | 1 | 5.39 | 5.39 |
| IMU 1 | ICM-45686 | 1 | 5.26 | 5.26 |
| IMU 2 | BMI088 | 1 | 5.92 | 5.92 |
| IMU 3 | IIM-42653 | 1 | 5.96 | 5.96 |
| Baro 1 | BMP390 | 1 | 3.30 | 3.30 |
| Baro 2 | MS561101BA03-50 | 1 | 11.56 | 11.56 |
| Magnetometer | MMC5983MA | 1 | 3.32 | 3.32 |
| CAN FD | TCAN1462DRQ1 | 2 | 1.49 | 2.98 |
| FRAM | FM25V02A-G | 1 | 8.75 | 8.75 |
| microSD | ST11S008V4HR2000 | 1 | 2.61 | 2.61 |
| Input selector | TPS2121RUXR | 1 | 2.44 | 2.44 |
| eFuse | TPS259470ARPWR | 2 | 1.82 | 3.64 |
| LDOs | TLV75733P ×1, TPS7A2033P ×2 | 3 | ~0.35 | 1.04 |
| ESD | PESD2CANFD24V ×2, TPD4E05U06 ×4 | 6 | — | 4.06 |
| Heater | MOSFET, resistors (unv) | — | — | ~2 |
| Passives, crystals | estimate | — | — | ~25 |
| Connectors | JST-GH, estimate | — | — | ~30 |
| **Total** | | | | **≈ $145** |

**PCB:** 6-layer, about 60 × 45 mm (assumed). OSH Park 6-layer is $15 per square inch for 3 boards, about $63 ([pricing](https://docs.oshpark.com/services/)); JLCPCB or PCBWay 6-layer impedance-controlled, 5 boards, roughly $50 to $150 plus a $10 to $20 steel stencil (unv).

---

## 2. Sensor nodes (DroneCAN)

All nodes share one MCU and CAN design so one firmware base serves every board.

### Node MCU, CAN, and firmware

| Part | Package | Assy | $ | Notes |
| --- | --- | --- | --- | --- |
| **STM32F412** | CEU6: UFQFPN-48 7 × 7 mm (M); RGT6: LQFP-64 (E/M) | M or E/M | 8.19 | Same MCU as ARK nodes, so PX4 `uavcannode` builds work ([PX4 docs](https://docs.px4.io/main/en/dronecan/px4_cannode_fw)). **CEU6 is out of stock until 30 Nov 2026, 52-week lead.** Prefer the LQFP RGT6 if stocked. |
| STM32G474RET6 (alternate) | LQFP-64 10 × 10 mm | E | 10.00 | Native CAN FD; same part as the pilot controls board; would need a new node build |
| TI TCAN1044VDRBRQ1 | VSON-8 3 × 3 mm | M | 2.06 | In stock |
| NXP TJA1051T/3 | SOIC-8 | E | unv | Easiest to hand-solder |

Firmware: PX4 `uavcannode` if the flight stack is PX4, ArduPilot AP_Periph if ArduPilot, or libcanard directly.

### 2.1 GNSS and compass node

| Part | Specs | Package | Assy | $ | PX4 / ArduPilot |
| --- | --- | --- | --- | --- | --- |
| **u-blox ZED-F9P-04B** | GPS/GLONASS/Galileo/BeiDou L1/L2; 1 cm RTK, ~2.5 m standalone; 20 Hz RTK; 68 to 130 mA | 54-LGA module 22 × 17 mm | M | 127.42, in stock | Y / Y |
| u-blox ZED-X20P-00B (backup) | All-band L1/L2/L5/L6, RTK/PPP-RTK | 54-LGA 22 × 17 mm | M | 186.98, 20-week lead | PX4 Y; ArduPilot (unv) |
| u-blox NEO-M9N-00B (subscale, low cost) | Single band, no RTK | Castellated module | E | 15.44 | Y / Y |
| u-blox MAX-M10S-00B | Single band, low power | Castellated LCC | E | 11.42 (unv) | Y / Y |
| Quectel LG290P | Quad-band, RTK | LCC (unv) | E/M | ~82 (unv) | unv |

| Magnetometer | Package | Assy | $ | Use |
| --- | --- | --- | --- | --- |
| **ST IIS2MDCTR** | 12-LGA 2 × 2 mm | M | 2.43 to 3.25 | Subscale and first boards |
| **PNI RM3100** | Coils plus driver ASIC | E/M (unv) | ~25 each (10 min); RM3100-CB $50 | Full scale: lowest noise, used on CubePilot Here4 |
| Memsic MMC5983MA | 16-LGA 3 × 3 mm | M | 2.93 | Alternate with degaussing |

Antenna: u-blox ANN-MB-00 L1/L2 patch (~$65, unv) or Tallysman HC882 dual-band helical (unv). Match antenna bands to the GNSS variant.

Reference: [ARK RTK GPS](https://arkelectron.com/product/ark-rtk-gps/) is open hardware ([GitHub](https://github.com/ARK-Electronics/ARK_RTK_GPS)): ZED-F9P, STM32F412CEU6, and on-board magnetometer, barometer, and IMU.

| GNSS node BOM | Qty | Unit $ | Ext $ |
| --- | ---: | ---: | ---: |
| ZED-F9P-04B | 1 | 127.42 | 127.42 |
| IIS2MDCTR | 1 | 2.43 | 2.43 |
| STM32F412 | 1 | 8.19 | 8.19 |
| TCAN1044VDRBRQ1 | 1 | 2.06 | 2.06 |
| ANN-MB-00 antenna (unv) | 1 | 65.00 | 65.00 |
| Regulator, passives, JST-GH, u.FL (unv) | lot | — | 13.00 |
| **Total** | | | **≈ $218** |

### 2.2 Barometer node

| BOM | Qty | Unit $ | Ext $ |
| --- | ---: | ---: | ---: |
| Bosch BMP581 (10-LGA 2 × 2 mm, M, 26,994 in stock) | 2 | 3.07 | 6.14 |
| STM32F412 | 1 | 8.19 | 8.19 |
| TCAN1044VDRBRQ1 | 1 | 2.06 | 2.06 |
| Enclosure foam, passives (unv) | lot | — | 11.00 |
| **Total** | | | **≈ $27** |

Backup sensor: TE MS5611 (larger, easier to solder, flight-proven on the Here4).

### 2.3 Optical flow and height node

| Part | Specs | Package | Assy | $ |
| --- | --- | --- | --- | --- |
| **PixArt PAA3905E1-Q** | 35 × 35 px, 126 Hz, works to 5 lux, 7.2 rad/s | 12-pin LGA (unv) | M | unv; **out of stock, 8 to 32 week lead** |
| **Broadcom AFBR-S50LV85D** | 30 m range, 12.4° × 6.2° FOV, rated for 200k lux sunlight, 5 V / 33 mA | Module (unv) | M | 66.90 |
| ST VL53L1X / VL53L8CX | 4 m dark, much less in sun / 8 × 8 zones, 2.8 m at 5,000 lux | Optical LGA | M | 6.63 / 8.56 |
| PixArt PMW3901MB | Needs > 60 lux | Chip-on-board with lens | **H**, no hand option | no authorized distributor |

Only the AFBR sensor is fit for outdoor height over grass; VL53 sensors are indoor or very low altitude. Flow velocity limit is roughly 7.2 × height (m/s), before estimator limits.

Reference: [ARK Flow](https://arkelectron.com/product/ark-flow/) ($250, 5 g, BSD-3 open hardware, [GitHub](https://github.com/ARK-Electronics/ARK_Flow)) uses the PixArt PAW3902 and AFBR-S50LV85D. **Buy one ARK Flow for the subscale drone** while the club board is designed, given PAA3905 supply.

| Flow node BOM | Qty | Unit $ | Ext $ |
| --- | ---: | ---: | ---: |
| PAA3905E1-Q (unv) | 1 | ~20 | 20.00 |
| AFBR-S50LV85D | 1 | 66.90 | 66.90 |
| ICM-42688-P (unv) | 1 | ~7 | 7.00 |
| STM32F412 | 1 | 8.19 | 8.19 |
| TCAN1044VDRBRQ1 | 1 | 2.06 | 2.06 |
| Passives, connectors (unv) | lot | — | 10.00 |
| **Total** | | | **≈ $114** |

### 2.4 IMU vibration logger (test tool)

| BOM | Qty | Unit $ | Ext $ |
| --- | ---: | ---: | ---: |
| Teensy 4.1 with microSD (unv) | 1 | 31.50 | 31.50 |
| ICM-42688-P breakout, raw high-rate data for vibration spectra (unv) | 1 | ~20 | 20.00 |
| CEVA BNO085 breakout (Adafruit #4754), optional attitude logging | 1 | 29.50 | 29.50 |
| microSD card (unv) | 1 | 8 | 8.00 |
| **Total** | | | **≈ $89** |

### 2.5 Power monitor node

For 18S packs (75.6 V maximum).

| Part | Key limit | Package | Assy | $ |
| --- | --- | --- | --- | --- |
| **TI INA228** (I²C) / INA229 (SPI) | 20-bit, −0.3 to 85 V common mode | VSSOP-10 | E/M | ~5; INA229 out of stock to Dec |
| TI AMC3330 + AMC3302 (isolated backup) | Isolated ±1 V input with built-in DC/DC, 4,250 V rms | SO-16 | E | unv |
| ADI LTC2992 | 100 V dual monitor (unv) | MSOP-16 (unv) | E | unv |

Low-side 100 µΩ shunt: 30 mV and 9 W at 300 A; candidates Vishay WSBE8518 or Isabellenhütte BVR (unv). Bus voltage through a divider: 75.6 V leaves only ~10 V below the INA228 rating.

| Power node BOM | Qty | Unit $ | Ext $ |
| --- | ---: | ---: | ---: |
| INA228 (unv) | 1 | ~5 | 5.00 |
| 100 µΩ shunt (unv) | 1 | ~15 | 15.00 |
| TI LM5164 100 V buck (unv) | 1 | ~2.50 | 2.50 |
| STM32F412 | 1 | 8.19 | 8.19 |
| TCAN1044VDRBRQ1 | 1 | 2.06 | 2.06 |
| Lugs, TVS, passives (unv) | lot | — | 15.00 |
| **Total** | | | **≈ $48** |

---

## 3. Safety monitor board

Independent of the flight controller: own IMU, own isolated power and reset, reviewed in full at design review (Article X, Section 10).

### FPGA

| Part | Config | Package | Assy | Temp | $ | Tools |
| --- | --- | --- | --- | --- | --- | --- |
| **Microchip IGLOO2 M2GL010-TQG144I** (primary) | Flash, instant-on, configuration immune to radiation upsets; 12k logic elements, 84 I/O | LQFP-144 20 × 20 mm, 0.5 mm | **E** | −40 to 100 °C | 32.54 | Libero SoC, free Silver license ([Microchip](https://onlinedocs.microchip.com/oxy/GUID-DED68D42-4F99-40F3-A46A-FD5607E13490-en-US-9/GUID-1A762DDA-2968-4A3D-959B-D7A8E6DA2600.html); M2GL010 coverage unv) |
| **Lattice MachXO2 LCMXO2-7000HC-4TG144I** (backup) | Flash, instant-on | TQFP-144, 0.5 mm | E | industrial (-I suffix) | ~24 | Lattice Diamond, free |
| Lattice MachXO3D LCMXO3D-9400HC-5SG72C | Flash, dual boot | QFN-72 10 × 10 mm | M | 0 to 85 °C | 31.57 | Diamond, free |
| Lattice iCE40UP5K-SG48I | SRAM + external flash; configuration can be upset | QFN-48 7 × 7 mm | M | −40 to 100 °C | 11.11, **out of stock to Jan 2027** | Open source (Yosys, nextpnr) |

Other MachXO3D and larger IGLOO2 parts are BGA only (H).

### Prototyping boards

| Board | FPGA | $ | Use |
| --- | --- | --- | --- |
| [iCEBreaker V1.1a](https://1bitsquared.com/products/icebreaker) | iCE40UP5K | 79.95 | Learn and write the monitor logic in plain Verilog with open tools, then port to IGLOO2 |
| Lattice LCMXO3D-9400HC-B-EVN | MachXO3D | 95.00 | Alternate |
| Club IGLOO2 LQFP-144 breakout | M2GL010 | parts only | Cheaper than the $750, out-of-stock IGLOO2 eval kit; needs a FlashPro5 programmer (unv) |

### Rest of the board

| Function | Part | Package | Assy | $ |
| --- | --- | --- | --- | --- |
| Independent IMU | **Murata SCH16T-K01-1**: 6-axis, AEC-Q100, SafeSPI with error checking, 0.5 °/h bias instability (backup ST ASM330LHHXTR, LGA, $8.93) | 24-BSOP, leaded | E | 63.21 |
| Window watchdog and supervisor | **TI TPS3851G33SDRBR**: catches a monitor kicking too fast or too slow (backup Maxim MAX6746, SOT23-8, E) | VSON-8 3 × 3 mm | M | 1.43 |
| Isolated power | **RECOM REM3-1205S/A**: 9 to 18 V in, 5 kV isolation, 5 V 3 W (backup RECOM RO-1205S, $7.41). Diode-OR from the 12 V rail and a small separate battery, plus a supercap hold-up (unv) | DIP-24 through-hole | E | 67.30 |
| Contactor coil driver (×4) | **Infineon BTS70021EPPXUMA1**: 2.6 mΩ, 21 A, current-sense diagnostics, AEC-Q100 (backup TI TPS1HB08-Q1, unv). GX14 12 V coil pulls up to 3.9 A for 75 ms, then holds ~0.23 A | TSDSO-14 exposed pad | M | 2.90 |
| Heartbeat input isolation (×2) | **TI ISO7741QDWRQ1**: 4-channel, 5 kV rms (backup ADI ADuM1401); **only 21 in stock** | SOIC-16W | E | 4.25 |
| CAN FD (×2) | TI TCAN1462VDRQ1 | SOIC-8 | E | 1.52 |

**Contactor wiring:** the monitor's high-side switch sits **in series** with the flight controller's coil driver and the hardwired E-stop, so any one of them opens the contactor.

**Parachute trigger:** the [Galaxy GRS 3 270](https://www.galaxysky.cz/grs-3-270-60m2-p58-en) supports mechanical and electrical activation with a dual primer. **The electrical initiator's resistance and firing currents are not published; request them from Galaxy and order the electric version.** Keep the pilot's mechanical handle primary. Firing circuit practice (unv against Galaxy): physical arm/safe switch that shorts the initiators in safe, one channel per primer with a high-side and low-side FET in series on separate enables, continuity checks well below the no-fire current, and a firing capacitor that only charges when armed. Minimum rescue height is 40 m at 65 km/h forward speed; more from a hover.

| Safety monitor BOM | Qty | Unit $ | Ext $ |
| --- | ---: | ---: | ---: |
| M2GL010-TQG144I | 1 | 32.54 | 32.54 |
| SCH16T-K01-1 | 1 | 63.21 | 63.21 |
| TPS3851G33SDRBR | 1 | 1.43 | 1.43 |
| REM3-1205S/A | 1 | 67.30 | 67.30 |
| BTS70021EPPXUMA1 | 4 | 2.90 | 11.60 |
| ISO7741QDWRQ1 | 2 | 4.25 | 8.50 |
| TCAN1462VDRQ1 | 2 | 1.52 | 3.04 |
| LDOs 3.3 / 2.5 / 1.2 V (unv) | 3 | 1.00 | 3.00 |
| Oscillator (unv) | 1 | 1.50 | 1.50 |
| Low-side coil FETs (unv) | 4 | 1.00 | 4.00 |
| Parachute firing FETs (unv) | 4 | 1.50 | 6.00 |
| Supercap hold-up (unv) | 1 | 5.00 | 5.00 |
| Connectors, passives, TVS, fuses (unv) | lot | — | 65.00 |
| **Total** | | | **≈ $272** |

Prototyping extras: iCEBreaker $79.95, SCH16T evaluation board $87.04.

---

## 4. Pilot controls and cockpit

Two independent channels (MCU, ADC, power) read the pilot's inputs.

| Function | Part | Package | Assy | $ |
| --- | --- | --- | --- | --- |
| Side stick (roll, pitch) | **APEM HF22S10** 2-axis Hall joystick; prefer APEM's dual-sensor redundant variant (part number unv) | Grip | — | 278.39 |
| Throttle / yaw | APEM HF22Y10 1-axis (0 in stock) | Grip | — | 224.57 |
| MCU (×2) | **ST STM32G474RET6**, native CAN FD | LQFP-64 10 × 10 mm | E | 10.00 |
| ADC (×2) | **TI ADS131M04IPWR**, 24-bit, 4 channels (backup TI ADS1115) | TSSOP-20 | E | 7.14 |
| CAN FD (×2) | TI TCAN1462VDRQ1 | SOIC-8 | E | 1.52 |
| Channel power (×2) | RECOM RO-1205S isolated 1 W | SIP | E | 7.41 |
| Display | **Newhaven NHD-7.0-800480FT-CSXP-T**: 7 in, 780 nits, FT812 controller over SPI (drivable from the G474); backup NHD-7.0-800480AF-ASXP, 1000 nits, needs an STM32H7 with LTDC; **both low stock** | Module | — | 84.30 |
| E-stop | **IDEC XA1E-BV3U02-R**, 2 NC contacts, wired straight into the contactor coil loop (backup Schneider XB5) | Panel | — | 76.67 |
| Guarded toggles, HV key switch, piezo alarm | unv | Panel | — | ~98 |

| Pilot controls and cockpit BOM | Qty | Unit $ | Ext $ |
| --- | ---: | ---: | ---: |
| APEM HF22S10 | 1 | 278.39 | 278.39 |
| APEM HF22Y10 | 1 | 224.57 | 224.57 |
| STM32G474RET6 | 2 | 10.00 | 20.00 |
| ADS131M04IPWR | 2 | 7.14 | 14.28 |
| TCAN1462VDRQ1 | 2 | 1.52 | 3.04 |
| RO-1205S | 2 | 7.41 | 14.82 |
| NHD-7.0-800480FT-CSXP-T | 1 | 84.30 | 84.30 |
| IDEC XA1E-BV3U02-R | 1 | 76.67 | 76.67 |
| Guarded toggles (unv) | 2 | 25.00 | 50.00 |
| HV key switch (unv) | 1 | 40.00 | 40.00 |
| Piezo alarm (unv) | 1 | 8.00 | 8.00 |
| Regulators, passives, connectors (unv) | lot | — | 40.00 |
| **Total** | | | **≈ $854** |

---

## 5. Perception and companion computer

Advisory only on the piloted vehicle: feeds the pilot display, ground station, and logs, never motor command. Prices reflect 2026 memory-driven increases.

### Companion computer

| Part | Compute | Cameras / mounting | Mass / power | $ |
| --- | --- | --- | --- | --- |
| **NVIDIA Jetson Orin NX 16GB** module (900-13767-0000-000), full scale | 100 TOPS, 8 × Cortex-A78AE | Up to 8 CSI lanes (unv); 260-pin SO-DIMM socket TE 2309413-1, 0.5 mm, visible leads, hand-reflowable | ~28 g module (unv), 10 to 25 W | ~700 to 900 (unv) |
| **Jetson Orin Nano Super Dev Kit 8GB**, subscale | 67 TOPS | 2 × 2-lane CSI; same software as Orin NX | ~175 g (unv), 7 to 25 W | 399, backordered |
| ModalAI VOXL 2 (fallback) | QRB5165, 15 TOPS, VIO included | 6 MIPI inputs, no carrier needed | 16 g, up to 7 W | 1,299.99 |
| AMD Kria KV260 kit (FPGA experiments) | Zynq UltraScale+ | Keep as an off-the-shelf kit; a custom K26 carrier needs X-ray (hidden-joint Samtec connectors) | ~250 g (unv) | 249 |
| Raspberry Pi 5 8GB + Hailo-8L | 13 TOPS | 2 CSI | ~70 g (unv) | 145 + 70 |

Orin carrier design guide: [NVIDIA DG-10931](https://www.mouser.com/pdfDocs/Jetson_Orin_NX_Series_and_Orin_Nano_Series_Design_Guide_DG-10931-001_v11.pdf). The carrier needs a fab-made, impedance-controlled 6+ layer PCB (unv).

### Cameras

| Part | Resolution / rate | Interface | Trigger | $ |
| --- | --- | --- | --- | --- |
| **Arducam OV9281** (primary, VIO pair) | 1280 × 800 at 60 fps, mono, global shutter | MIPI CSI-2 | External trigger pins | ~50 to 70 (unv) |
| e-con e-CAM25_CUONX AR0234 (backup) | 1920 × 1200, up to 120 fps (unv) | MIPI, Orin native | Yes (unv) | ~300 (unv) |
| Raspberry Pi Global Shutter Camera (IMX296) | 1456 × 1088 at 60 fps | CSI | XVS pads (unv) | 50 |

### Depth and obstacle sensing

| Part | Range / FOV | Sunlight | Mass | $ |
| --- | --- | --- | --- | --- |
| **Luxonis OAK-D Pro W** | Active stereo, ~127° diagonal (unv), onboard IMU | Passive stereo works outdoors; projector washes out | ~91 g (unv) | 499 to 549 |
| **LightWare SF45/B** | 0.2 to 50 m, 320° scan | Good | 59 g | 449, sold out |
| **Livox Mid-360** (full scale mapping) | 360° × 59°, 200k points/s | Rated to 100 klx | 265 g | 3,800, sold out |
| Benewake TF03-180 (altitude) | 0.1 to 180 m | Strong | 77 to 86 g | 274.95 |
| RealSense D455 (backup) | 0.6 to 6 m, 87° × 58° | Moderate | ~390 g (unv) | 419 |

### Time sync, calibration, links, software

- **VIO needs:** global shutter, IMU at 200 to 1000 Hz, camera-to-IMU timestamp error under ~1 ms. Trigger the OV9281s from an MCU PWM and log the IMU sample on the same edge, or use the OAK's internal hardware-synced IMU.
- **Calibration:** Kalibr camera calibration, then camera-IMU calibration with an IMU noise model from allan_variance_ros; recalibrate after any mount change.
- **Video downlink:** SIYI HM30 on the subscale (from $287.70, ~150 ms latency, [SIYI](https://shop.siyi.biz/products/siyi-hm30-digital-image-transmission)); CubePilot Herelink V1.1 on full scale (MAVLink plus HDMI, 20 km, controller $918.99, backordered). DJI O3/O4 is not suitable (no companion-computer video input or MAVLink).
- **Software:** OpenVINS (GPLv3), VINS-Fusion (GPLv3), ORB-SLAM3 (GPLv3), RTAB-Map (BSD-3, unv). PX4 accepts VIO over uXRCE-DDS (`EKF2_EV_CTRL`); ArduPilot via `VISO_TYPE`. On the piloted vehicle, VIO goes to logs and comparison only.

### Perception BOMs

| Subscale | Qty | Unit $ | Ext $ | Mass |
| --- | ---: | ---: | ---: | ---: |
| Jetson Orin Nano Super Dev Kit | 1 | 399 | 399 | 175 g (unv) |
| Arducam OV9281, triggerable | 2 | 60 (unv) | 120 | 30 g (unv) |
| OAK-D Pro W | 1 | 499 | 499 | 91 g (unv) |
| LightWare SF45/B | 1 | 449 | 449 | 59 g |
| SIYI HM30 combo | 1 | 1,000 (unv) | 1,000 | 110 g (unv) |
| NVMe 256 GB, 5 V/5 A BEC | 1 | 65 (unv) | 65 | 23 g |
| AprilGrid target, mounts, cables | 1 | 150 (unv) | 150 | 80 g |
| **Total** | | | **$2,682** | **~568 g** |

| Full scale | Qty | Unit $ | Ext $ | Flown mass |
| --- | ---: | ---: | ---: | ---: |
| Jetson Orin NX 16GB module (1 spare) | 2 | 900 (unv) | 1,800 | 28 g |
| Club Orin carrier (fab, socket, parts) | 2 | 300 (unv) | 600 | 80 g (unv) |
| Orin heatsink and fan | 2 | 60 (unv) | 120 | 90 g (unv) |
| AMD KV260 (FPGA preprocessing) | 1 | 249 | 249 | 250 g (unv) |
| OAK-D Pro W (front, left, right) | 3 | 499 | 1,497 | 273 g (unv) |
| OV9281 VIO pair | 2 | 60 (unv) | 120 | 30 g |
| Livox Mid-360 | 1 | 3,800 | 3,800 | 265 g |
| Benewake TF03-180 | 1 | 275 | 275 | 80 g |
| Herelink V1.1 (controller + air unit ~$450 unv) | 1 | 1,369 | 1,369 | 90 g (unv) |
| Harness, enclosure, target | 1 | 500 (unv) | 500 | 300 g (unv) |
| **Total** | | | **$10,330** | **~1.49 kg** |

---

## 6. Hand assembly and qualification

### Tools (estimates, unv)

| Tool | Estimate |
| --- | --- |
| Reflow hot plate or small reflow oven | $100 to $500 |
| Hot air rework station | $100 to $300 |
| Stereo microscope or inspection camera | $300 to $800 |
| Current-limited bench power supply | $100 to $300 |
| Oscilloscope | $400 to $1,000 |
| Stencil frame, solder paste, flux, tweezers, ESD mat | $150 to $250 |
| USB-to-CAN adapter, SWD/JTAG debuggers, FlashPro5 (IGLOO2) | $200 to $400 |
| **Total** | **≈ $1,350 to $3,550** |

Check what Texas Inventionworks and department labs already have before buying.

### Assembly and bring-up checklist (every board)

1. Design review before ordering (Article X, Section 3 for anything that may fly).
2. Order boards with a steel stencil; inspect bare boards.
3. Paste, place, reflow; inspect every leaded joint under the microscope.
4. Continuity check between power rails and ground **before** first power.
5. First power on a current-limited supply at low limit; check every rail.
6. Program the MCU or FPGA; read every sensor's ID register (the only way to confirm hidden LGA and QFN joints).
7. Functional test against the board's test procedure; log results in the hardware revision log.
8. For flight-critical boards: qualification at or above operating current, voltage, temperature, and vibration, then documented run time on the subscale drone, HIL rig, and unmanned article before any piloted use.

---

## 7. Supply risks

| Part | Issue | Mitigation |
| --- | --- | --- |
| TDK ICP-20100, ICP-10111, Infineon DPS310 | End of life | BMP390, MS5611, BMP581 |
| TDK IIM-42652 | Out of stock to Aug 2027 | IIM-42653 |
| TDK ICM-45686, ST IIS2MDC, Bosch BMP581 (some distributors) | Out of stock at time of search | Backups listed; order early |
| STM32F412CEU6 | 0 stock to 30 Nov 2026, 52-week lead | LQFP RGT6 or STM32G474 |
| PixArt PAA3905E1-Q | Out of stock, 8 to 32 week lead | ARK Flow for subscale |
| Lattice iCE40UP5K | Out of stock to Jan 2027 | Buy an iCEBreaker board now |
| TI ISO7741QDWRQ1 | 21 units in stock | ADuM1401 |
| Newhaven 7 in displays, APEM HF22Y10 | Low or zero stock | Order early, backups listed |
| Jetson Orin Nano kit, SF45/B, Mid-360, Herelink | Backordered or sold out | Confirm lead times with Arrow, Seeed, IR-Lock |
| Molex 5031821852 microSD socket | Obsolete | JAE ST11S008V4HR2000 |

---

## 8. Funding summary

| Line item | Estimate |
| --- | ---: |
| Flight controller: 3 boards of parts ($435) + spares + 6-layer fab | ≈ $600 |
| Sensor nodes: GNSS, barometer, flow, power, vibration logger (one each) | ≈ $500 |
| Sensor nodes: second full set for the full-scale vehicle | ≈ $410 |
| Safety monitor: 2 boards + iCEBreaker + SCH16T eval board | ≈ $710 |
| Pilot controls and cockpit | ≈ $854 |
| ARK Flow (buy for subscale while club board is designed) | ≈ $250 |
| Perception, subscale | ≈ $2,682 |
| Perception, full scale | ≈ $10,330 |
| PCB fabrication and stencils, all other boards | ≈ $600 (unv) |
| Assembly and bring-up tools | ≈ $1,350 to $3,550 |
| **Total avionics and perception program** | **≈ $18,300 to $20,500** |

This is in addition to the vehicle hardware estimate in [`baseline.md`](baseline.md) (≈ $20,000 to $22,000, which assumed commercial avionics). The full-scale perception package is the largest single line and the easiest to phase: the subscale set proves the software first, and the Livox Mid-360 ($3,800) can wait until site mapping is actually needed.
