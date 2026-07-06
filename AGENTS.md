# mojos — the MojOS flake

The OS piece of the Mojo project: a NixOS system built around mojo-agent as the
primary interface. This repo becomes the flake that declares Clarke's entire system.

**Before any work here:** read the thinking repo —
`~/projects/mojo/vision.md` and `~/projects/mojo/philosophy.md` — then the current
milestone in [V0.1.md](V0.1.md). OS-layer design/build thinking produced in a
session here gets captured in this repo's own [devlog.md](devlog.md);
project-wide vision decisions still go back to the thinking repo
(`~/projects/mojo/devlog.md` / `ideas.md`).

## Current stage

Pre-flake. v0.1 (see [V0.1.md](V0.1.md)): a minimal flake, VM-proven, built to run
Hermes Agent — then daily-driver tooling and modes built *with* Hermes's help, then
installed on Clarke's laptop as daily driver, then the PC once he's back at uni. No
installable code exists yet; nothing in this repo may claim otherwise. No hard
feature-complete deadline — see V0.1.md.

## Hard constraints

- **Design for Hermes first.** v0.1's job is running Hermes Agent, not modes or rice
  on their own — those come after, built with the agent's help.
- **Clarke is learning Nix — that's a deliverable, not friction.** Sessions explain
  the concepts behind every non-obvious Nix construct and let Clarke drive design
  decisions. Don't generate a finished flake over his head.
- **VM before metal.** Nothing is declared "working" until it boots via
  `nixos-rebuild build-vm`; nothing touches real hardware until proven there.
- **Open source first.** Stylix, home-manager, established flake patterns — reuse
  before writing.
- **Multi-host from day one.** Laptop now, PC later — per-host config separated
  from shared modules from the first commit.
- Docs only when earned — no scaffolded directories.
