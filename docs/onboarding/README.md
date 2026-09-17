# Onboarding

How someone new becomes useful on this vehicle. Everything here is public and self-serve: **you do not need to be a club member, or have any access we have to grant you, to start.** Clone the repository and begin.

New members work the same ladder. It is deliberately not a tutorial with an answer key: you calculate, you simulate, you design, you build, and you measure, and only then do you compare against the reference design.

| Track | Status | Start here |
| --- | --- | --- |
| **Electrical: power board ladder** | Active | [electrical.md](electrical.md) |
| **Software: telemetry ladder** | In development, published when the Electrical ladder has been run once | — |

---

## What it looks like

**Level 1, weeks 1 to 3: a 6S to 5 V buck converter.** Size the parts by hand, simulate them in LTspice, draw the schematic and lay out the board in KiCad, hand-assemble it, and measure it against your own predictions.

**Level 2, weeks 4 to 8: redesign it** into something the vehicle actually needs, either a protected and monitored avionics power module or a constant-current strobe driver. Every required feature exists to teach a fundamental that the club's real boards depend on.

The first three weeks need **nothing but a laptop**. Calculations, simulation, schematic, and layout are all free software. Fabrication, parts, and bench time come through the club.

---

## What is in this folder

| Path | What it is |
| --- | --- |
| [`electrical.md`](electrical.md) | The Electrical ladder: specs, lessons, labs, deliverables, and review gates |
| [`git-workflow.md`](git-workflow.md) | Git from zero: clone, branch, commit, pull request, and how to get updated instructions |
| [`tools/ltspice.md`](tools/ltspice.md) | Installing LTspice and every simulation the ladder asks for, with the directives to type in |
| [`template/`](template/) | Your starting point: worksheet, simulation tables, results tables, and empty `sim/`, `kicad/`, `test/`, `photos/` folders |
| [`reference/`](reference/) | The finished example, for comparison **after** your own attempt |

Related: [PCB design guide](../hardware/pcb-design-guide.md) · [teams and projects](../teams.md) · [safety requirements](../safety/vehicle-safety-requirements.md)

---

## Start on your own, right now

```bash
git clone https://github.com/LonghornEVTOL/longhorn-evtol.git
cd longhorn-evtol/docs/onboarding
```

Read [`electrical.md`](electrical.md), copy [`template/level-1-buck/`](template/level-1-buck/) somewhere you can work, install [LTspice](tools/ltspice.md) and KiCad, and start on the worksheet. `git pull` whenever you want the newest instructions and, later, the reference design.

Doing this before you apply is a good use of your time and we will ask you about it. Nothing here is graded and nothing is due; work as far as you get.

## Once you are a member

You get a workspace in the club's private repository, an onboarding issue with your gates, a mentor who reviews your pull requests, lab and bench access, and your board on the group fab order. Ask an officer to add you to the GitHub organization; the [git workflow](git-workflow.md) covers the rest.

Applications and dates: [longhorn-evtol.vercel.app](https://longhorn-evtol.vercel.app)
