# Modes

Wishlist, not commitments — shrinks as things get built, doesn't move anywhere else.

## Principle

A mode changes **behavior** first — what's visible, what interrupts you, what's
running, what shortcuts are live. The look follows from that. A mode that's only a
different palette is decoration wearing a mode's name.

Corollary: not everything is a mode. Cross-cutting settings that apply regardless of
which mode is active (e.g. light/dark by time of day, à la `darkman` — see misc.md)
are knobs, not modes.

## The three (decided 2026-07-21)

**Regular / classic** — MojOS being itself. The workshop vibe, good rice, cool-looking,
just the right amount of nerdy. This is the default and what ships first — no
mode-switching mechanism is needed for this one to exist; it's just what the daily
driver looks like out of the gate.

**Second mode (name TBD — "muggle mode" proposed and rejected)** — for when other
people are watching the screen. Not boring — still has a real background/vibe, still
looks intentional — just restrained on nerd-signaling: no visible widgets, no crazy
tiling, no terminal (or none visible). Reads more like a conventional OS (closer to
Mac/Windows) than a riced Linux box. Screen-share-safe.

**Coding** — dialed in. Opens the coding app set (editor, terminal, browser) on entry.
Lots of terminal. Dense/tight layout, notifications mostly silenced except urgent.

## Session state — two different things, only the first is in scope for v0.1

- **Fixed declarative launch:** each mode declares a known app/workspace layout that
  opens on entry. Reproducible, matches the flake's declarative model. Every mode
  gets its own, not just coding.
- **True session resume (later idea):** remembering exactly which windows/tabs/panes
  were open and restoring that. Real runtime state — fights impermanence, since root
  wipes every boot and this would need to live in `/persist`. Hyprland has no mature
  tool for this the way KDE does. Closest real answer for the terminal side
  specifically: a tmux session manager (`sesh`, or resurrect+continuum). GUI window
  resume is fuzzier.

## Also parked

- **Per-project coding mode** — own layout/app-set scoped to a specific project.

## Mechanism (see ricing.md)

Modes = declarative Stylix-variable swaps, not `wallust`-driven — keeps every mode
reproducible from the flake alone.

## Sequencing

None of this blocks the daily-driver milestone. Regular/classic alone is enough to
replace Arch. The switching mechanism plus the other two modes come whenever they
come — not urgent.
