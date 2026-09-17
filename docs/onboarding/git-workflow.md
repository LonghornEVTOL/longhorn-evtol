# Git Workflow for Onboarding

Onboarding runs through Git, so you finish it knowing the tool the club uses every day. You pull the instructions and the reference design from this repository, do your work on a branch, and open a pull request at each review gate.

If you have never used Git, work through this page once with your mentor, or on your own. After that, six commands cover everything.

There are two ways to do this, and you can start today either way:

| | **Not a member yet** | **Member, in the GitHub organization** |
| --- | --- | --- |
| Instructions and template | This public repository | Same |
| Where your work lives | Your own fork, or just your laptop | `onboarding/members/<username>/` in the private `internal` repository |
| Reviews | None, but keep your work; bring it to your interview | Pull request at every gate, reviewed by your mentor |

---

## One-time setup

```bash
# 1. Install Git, then tell it who you are
git config --global user.name "Your Name"
git config --global user.email "you@utexas.edu"

# 2. Clone this repository. No account or permission needed; it is public.
cd C:/dev            # or wherever you keep projects
git clone https://github.com/LonghornEVTOL/longhorn-evtol.git
```

That is all you need to read every instruction, open the template, and start Level 1.

**Optional but recommended:** install the [GitHub CLI](https://cli.github.com/) and run `gh auth login`. It makes pull requests a one-line command later.

---

## If you are not a member yet

Work in your own copy. Nothing is lost when you join: you bring the folder with you.

```bash
# Fork this repository on github.com (the Fork button), then:
git clone https://github.com/<your-username>/longhorn-evtol.git
cd longhorn-evtol
git switch -c onboarding

# copy the template into a folder of your own
cp -r docs/onboarding/template/level-1-buck my-onboarding
```

On Windows PowerShell, use `Copy-Item docs\onboarding\template\level-1-buck my-onboarding -Recurse` instead of `cp -r`.

Then work, commit, and push to your fork:

```bash
git add my-onboarding
git commit -m "Add feedback divider and inductor calculations"
git push -u origin onboarding
```

Keeping it in Git is the point: your commit history shows how you worked through the problem, and it is a good thing to show at an interview. You can also skip Git entirely and keep the folder on your laptop; just do not lose it.

To get new instructions later:

```bash
git remote add upstream https://github.com/LonghornEVTOL/longhorn-evtol.git
git fetch upstream
git merge upstream/main
```

---

## If you are a member

Your workspace lives in the club's private repository, `LonghornEVTOL/internal`, which needs organization access. Ask an officer if the clone fails.

```bash
cd C:/dev
git clone https://github.com/LonghornEVTOL/internal.git        # your work
git clone https://github.com/LonghornEVTOL/longhorn-evtol.git  # instructions, template, reference

cd internal
git switch main
git pull
git switch -c onboarding/maya-okonkwo          # your GitHub username

# scaffold your workspace from the template in the public repo
./onboarding/new-member.sh maya-okonkwo        # PowerShell: .\onboarding\new-member.ps1 -Username maya-okonkwo

git add onboarding/members/maya-okonkwo
git commit -m "Start Level 1 buck converter workspace"
git push -u origin onboarding/maya-okonkwo
```

### The daily loop

```bash
git pull                 # get other people's changes first
# ... work: fill in the README tables, add KiCad and LTspice files, photos, measurements ...
git add onboarding/members/maya-okonkwo
git commit -m "Add LTspice ripple and efficiency results"
git push
```

Commit whenever you finish something meaningful, not once at the end. Small commits with clear messages make reviews easy.

### At each gate, open a pull request

Gates are listed in your onboarding issue: design review (worksheet, simulation, schematic), layout review, bring-up sign-off, and the Level 2 reviews.

```bash
gh pr create --fill                        # or open the pull request on github.com
```

Then paste the link in your issue, ask your mentor for a review, fix what they comment on with another `git add`, `git commit`, `git push` (the pull request updates itself), and tick the gate once it is merged. Keep the same branch for the whole level.

---

## When the instructions change

Officers add lessons, labs, and reference material to this public repository while you are working. Pull them whenever an officer says something new is up, or about once a week:

```bash
cd C:/dev/longhorn-evtol
git pull
```

Members whose own work sits on a branch in `internal` also pull that repository's `main` into their branch:

```bash
cd C:/dev/internal
git switch main && git pull
git switch onboarding/maya-okonkwo
git merge main
```

Nothing of yours is lost: you only edit files inside your own folder, so this almost never conflicts.

---

## The reference design

When the finished example is published it appears in [`reference/`](reference/) here: the LTspice simulations, the KiCad project, photos of the assembled board, measured results, and tips from whoever built it first. `git pull` and it is there.

**Look at it after your own attempt at each step**, not before. The point is to design it yourself and then see how someone else solved the same problem.

---

## When Git complains

| Message | What it means | What to do |
| --- | --- | --- |
| `Updates were rejected because the remote contains work that you do not have` | Someone pushed to your branch | `git pull --rebase` then `git push` |
| `CONFLICT (content): Merge conflict in ...` | Two people edited the same lines | Open the file, keep the right text, delete the `<<<<<<<`, `=======`, `>>>>>>>` markers, then `git add <file>` and `git rebase --continue` |
| `fatal: not a git repository` | You are in the wrong folder | `cd` into the repository folder |
| `Permission denied`, or a login prompt on `internal` | Not signed in, or not in the organization | `gh auth login`, then ask an officer for access |
| You committed something huge or private | KiCad backups, LTspice `.raw` files, big photos, secrets | Tell a lead before pushing; `git reset HEAD~1` undoes the last commit and keeps your files |

---

## Rules

- **Never commit to `main` directly.** Work on a branch and open a pull request.
- **Only touch your own folder**, unless a lead asks otherwise.
- **Photos:** JPG or PNG under about 2 MB each. Resize before committing; Git keeps every version forever.
- **KiCad:** commit `.kicad_pro`, `.kicad_sch`, `.kicad_pcb`, and your BOM. Backup, autosave, and lock files are already ignored.
- **LTspice:** commit `.asc`, `.plt`, and plot PNGs in `sim/`. Never commit `.raw` files; they are enormous and are already ignored.
- **Never commit** personal data, passwords, or API keys.
- **Pull before you start** each session.

---

## Command cheat sheet

| Goal | Command |
| --- | --- |
| Get the latest | `git pull` |
| See what changed | `git status` and `git diff` |
| Stage and save work | `git add <path>` then `git commit -m "message"` |
| Send it to GitHub | `git push` |
| Start a branch | `git switch -c onboarding/<username>` |
| Switch branches | `git switch main` / `git switch onboarding/<username>` |
| Get new instructions onto your branch | `git switch main && git pull && git switch onboarding/<username> && git merge main` |
| Update a fork from the club repository | `git fetch upstream && git merge upstream/main` |
| Open a pull request | `gh pr create --fill` |
| See your history | `git log --oneline` |
| Undo the last commit, keep the files | `git reset HEAD~1` |
