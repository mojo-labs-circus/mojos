# Flake structure

Decided 2026-07-21, after comparing a handful of well-regarded personal multi-host
NixOS repos (maxclax/nix-dotfiles — minimal; totoroot/dotfiles — mature end state;
lukejcollins/nix-multi-system-flake; fortydeux/Fortydeux-NixOS-System-Flake).

- **Plain `nixpkgs.lib.nixosSystem`, no flake-parts.** flake-parts earns its keep for
  flakes targeting multiple platforms, or exporting reusable modules for other
  people's flakes. Neither applies — single arch (x86_64-linux), personal fleet, not
  a published library. It showed up in none of the personal-fleet repos surveyed.
- **`hosts/<hostname>/` directories**, not generic profiles — added when a machine
  actually exists, not built ahead of hardware. Matches the project's own convention
  of naming real machines (`nomadbaker`, `pearlybaker`) rather than placeholders.
- **`modules/` split by concern** (desktop/dev/hardware/security/services/shell/theme
  -style, à la totoroot) once duplication across hosts actually justifies it. Not on
  day one — start flat.
- **home-manager wired as a NixOS module** in the same host module list (`useGlobalPkgs
  = true`, `useUserPackages = true`), not standalone.
