# PCB Design Guide

How Longhorn eVTOL designs, reviews, and releases circuit boards. All boards are designed in **KiCad** and hand-assembled by the club. Part choices for each board are in [`../vehicle/avionics-parts.md`](../vehicle/avionics-parts.md).

---

## Tools

| Tool | Use |
| --- | --- |
| **[KiCad](https://www.kicad.org)** (latest stable release) | Schematic, layout, and fabrication outputs. Free and open source, runs on Windows, macOS, and Linux. |
| **Interactive HTML BOM** (KiCad plugin, install from KiCad's Plugin and Content Manager) | Clickable part placement map for hand assembly: highlights where each part goes on the board |
| **kicad-cli** (ships with KiCad) | Scripted export of Gerbers, drill files, schematic PDF, and BOM |
| **KiCad 3D viewer, STEP export** | Fit checks with Mechanical's CAD |

**One KiCad major version per board.** Record it in the board's README, and don't open a board in a newer major version without agreeing as a team: files saved by a newer version won't open in older ones.

---

## Repository layout

Each board gets its own folder, alongside the project folders already in the repo (for example `avionics-pcb/flight-controller/`):

```
avionics-pcb/flight-controller/
  README.md                  board purpose, KiCad version, status, owner, revision log
  flight-controller.kicad_pro
  flight-controller.kicad_sch
  flight-controller.kicad_pcb
  sheets/                    hierarchical schematic sheets (power.kicad_sch, imu.kicad_sch, ...)
  bom/                       BOM export (CSV) with manufacturer part numbers and DigiKey links
  fab/rev-A/                 released Gerbers, drill, schematic PDF, iBOM, STEP; only for tagged revisions
  test/                      bring-up procedure and results
```

Shared parts live in one club library, so every board uses the same symbols and footprints:

```
hardware-lib/
  longhorn.kicad_sym         symbols
  longhorn.pretty/           footprints
  3d/                        STEP models
```

Add the library to each project as a **project-specific** library with a relative path (`${KIPRJMOD}/../../hardware-lib/...`), not a global one, so the project opens the same way on everyone's computer.

---

## Library rules

- Use KiCad's built-in libraries for standard parts (resistors, capacitors, SOIC, LQFP). Put anything custom or modified in `hardware-lib`.
- Every symbol carries **Manufacturer**, **MPN**, and **DigiKey** fields. The BOM is generated from these fields, never typed by hand.
- Build footprints from the **manufacturer's recommended land pattern** in the datasheet, not a guess. A second person checks every new footprint against the datasheet before it's used on a board.
- For LGA and QFN parts, follow the datasheet's stencil recommendation for the exposed pad (usually a windowed paste pattern, not one big opening).

---

## Design rules for hand assembly

These follow from the assembly ratings in the parts doc.

| Rule | Why |
| --- | --- |
| No BGA, WLCSP, or 0.4 mm pitch parts | Joints can't be inspected without X-ray |
| 0603 passives by default, 0402 only where layout requires | Easier to place and inspect by hand |
| Prefer LQFP/TQFP over QFN when both exist | Leaded joints are visible |
| At least 0.5 mm clearance around fine-pitch parts for a rework nozzle | Hot air rework |
| Test points on every power rail, reset, SWD/JTAG, CAN, and key signals | Bring-up and debugging |
| Silkscreen: reference designators, pin 1 marks, polarity, rail voltages, board name and revision | Assembly without the schematic open |
| Fiducials are optional | No pick-and-place |
| SWD or JTAG header on every MCU and FPGA board | Programming and debugging |
| Power-entry protection: reverse polarity, TVS, fuse or eFuse | Survives bench mistakes |
| Mounting holes and connector positions agreed with Mechanical before routing | Fits the airframe |

**Fab constraints.** Set KiCad's board setup (track width, clearance, via size, layer stackup) from the chosen fab's published capabilities before routing, and keep the fab's stackup file in the board folder. Six layers is the default for the flight controller and companion carrier; two or four layers for sensor nodes and simple boards. Impedance-controlled boards (USB, Ethernet, MIPI camera lines, CAN FD) use the fab's impedance calculator for trace widths.

---

## Design review checklist

Every board gets a review before ordering. Flight-critical boards follow Article X, Section 3.

**Schematic**
- [ ] Electrical rules check (ERC) passes with no unexplained exceptions
- [ ] Every part has manufacturer, MPN, and DigiKey fields; all parts in stock or with a listed backup
- [ ] Power tree drawn: every rail's source, current budget, and protection
- [ ] Decoupling matches each chip's datasheet
- [ ] Every IC's pull-ups, straps, and unused pins handled per datasheet
- [ ] Connector pinouts match the interface control document

**Layout**
- [ ] Design rules check (DRC) passes against the fab's rules
- [ ] Footprints checked against datasheets by a second person
- [ ] Decoupling capacitors placed at the pins they serve
- [ ] High-current paths sized for their current and temperature rise
- [ ] Sensitive sensors (IMUs, barometers, magnetometer) away from switching regulators and high current; IMU mounting and heater island per the parts doc
- [ ] Ground planes continuous under high-speed signals
- [ ] Test points, SWD header, and silkscreen per the rules above
- [ ] 3D view checked against the mechanical model

**Release**
- [ ] Reviewer who did not design the board signs off
- [ ] Revision letter assigned, Git tag created (`flight-controller-rev-A`), outputs exported to `fab/rev-A/`

---

## Fabrication outputs

For each release, export into `fab/rev-X/`:

| File | How |
| --- | --- |
| Gerbers and drill files | KiCad Fabrication Outputs, or `kicad-cli pcb export gerbers` and `kicad-cli pcb export drill` |
| Schematic PDF | `kicad-cli sch export pdf` |
| BOM (CSV) | `kicad-cli sch export bom` using the MPN and DigiKey fields |
| Interactive HTML BOM | Interactive HTML BOM plugin |
| STEP model | `kicad-cli pcb export step` |

Order bare boards **with a steel stencil**. No pick-and-place files are needed for hand assembly.

---

## Git practices for KiCad

- Commit the `.kicad_pro`, `.kicad_sch`, and `.kicad_pcb` files and the club library. Local and backup files are ignored by the repo's `.gitignore`.
- **KiCad files don't merge well.** Only one person edits a given schematic sheet or layout at a time: say so in the team channel or claim it with a draft pull request.
- Split large schematics into hierarchical sheets so people can work on different sheets in parallel.
- Every revision sent to a fab gets a tag. Boards on the bench are always traceable to a tag and its revision log entry.

---

## Bring-up

Follow the assembly and bring-up checklist in [`avionics-parts.md`](../vehicle/avionics-parts.md#6-hand-assembly-and-qualification): inspect, check rails for shorts, power up current-limited, program, read every sensor's ID register, then test. Record results in the board's `test/` folder and its revision log.
