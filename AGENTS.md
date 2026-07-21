# mojos — the MojOS flake

Clarke's personal NixOS: one flake declaring every machine he runs, fully
declarative, git-versioned, reversible by design. Stands on its own — nothing here
depends on any other project existing. Built to be a genuinely good NixOS on its own
terms; that also happens to make it a good foundation for deeper integration later,
whenever that's ready.

**Before any work here:** read devlog.md's latest entry for where things actually
stand and what's next — every entry ends with one. Longer-term ideas live in
[ideas/](ideas/), organized by topic; check there before designing something from
scratch, and update it as thinking changes. Nothing in ideas/ is a commitment.

## Hard constraints

- **Build the substrate, not an agent.** No agent exists to run yet; don't block on
  one, don't hand-build one here either.
- **Clarke is learning Nix — that's a deliverable, not friction.** Explain the
  concepts behind every non-obvious Nix construct. Let Clarke drive design
  decisions — don't generate a finished flake over his head.
- **VM before metal, until metal is MojOS.** Nothing is declared "working" until it
  boots via `nixos-rebuild build-vm` — that's the bar before the first real install.
  Once MojOS is running on real hardware, changes get made and switched from within
  it directly; NixOS generations (and the wider reversibility architecture) are the
  safety net from that point on, not a VM.
- **Open source first.** Reuse established patterns (Stylix, home-manager, disko,
  etc.) before writing custom.
- **Multi-host, added as needed.** A host gets its own `hosts/<hostname>/` directory
  when it actually exists — not a generic placeholder, not built ahead of the
  hardware.
- Docs only when earned — no scaffolded directories.
