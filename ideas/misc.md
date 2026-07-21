# Misc

The interesting stuff that doesn't have a section yet.

- **kanata / keyd** — kernel-level keyboard remapping with layers, leader keys, works
  across every app, not per-WM.
- **darkman** — auto light/dark by sunrise/sunset, broadcasts a signal apps can hook
  into. A cross-cutting setting, not a mode (see modes.md) — should apply within
  whichever mode is active.
- **atuin** — searchable shell history that syncs *across machines*, not just local
  search. Genuinely nice once 2+ hosts exist.
- **Homepage / Homarr** — self-hosted dashboard aggregating service status/links. Fits
  once the server exists.
- **fastfetch, eza, bat, zoxide, starship** — still the standard modern CLI stack as
  of the last check.
- **systemd user timers** — the native mechanism for ambient automation (scheduled
  sync, cleanup, a scheduled mode-switch), no root needed. Prefer this over cron or a
  hand-rolled daemon.
- **Local DNS** (Pi-hole or plain dnsmasq) — makes the fleet feel like a real network,
  independent of whatever mesh tool ends up chosen.
