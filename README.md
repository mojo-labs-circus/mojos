# MojOS

My own personal NixOS: one flake declaring every machine I run — laptop, PC,
server, and short-lived "ghost" boots on borrowed hardware — fully declarative,
git-versioned. Every change lands as a NixOS generation, staged and safe to undo.

Built to be a genuinely good daily-driver system on its own terms: reversible, real
secrets management, real multi-host support. It's also a workshop with moods —
switchable modes that change how the system looks and behaves, not just its palette.

## What it's building toward

- **The same OS, anywhere.** One flake declares a fleet of owned machines (laptop,
  gaming PC, headless server) that all converge to the same source of truth via
  `nixos-anywhere` and `disko` — plus a Ventoy USB running a `nixos-generators`
  image that boots the whole system fresh on hardware I don't own. Wherever I am,
  whatever's actually in front of me, that's my OS.
- **Modes that change behavior, not just wallpaper.** Stylix-driven variable swaps
  that change notification policy, layout density, and what's even visible — a
  dialed-in coding mode, a normal-looking mode for company, MojOS being itself the
  rest of the time.
- **Reversibility as the actual safety net.** NixOS generations version the config,
  Snapper snapshots version real data on a persistent volume, an ephemeral tmpfs
  root wipes clean every boot — nothing survives unless it's explicitly declared.
  That's what makes it safe to change things fast.
- **Secrets handled with sops-nix**, not sitting in plaintext in a repo — wired in
  from day one, not bolted on later.
- **disko for declarative partitioning** — the whole disk layout, including a
  dual-booted Windows partition kept around as an escape hatch, is one Nix
  expression instead of a live `parted` session.

## Planned to be a good substrate for a future AI to work in with me

A digital counterpart, not just an assistant living outside a chat window.
Everything declared, everything reversible, secrets actually handled — that's what
I'd want in place before trusting anything more autonomous with real access to my
system. Not building that agent here. Just don't want to retrofit safety onto a
system that was never built with it in mind.

See [devlog.md](devlog.md) for where things stand and what's next, and
[ideas/](ideas/) for the longer-term wishlist.

## License

[MIT](LICENSE)
