# Sandboxing & security

Explicitly later work — not gating any early milestone. Not necessary until there's
something worth isolating (an agent, or untrusted code).

## Three tiers, by isolation strength

1. **systemd sandboxing** (`DynamicUser`, `ProtectSystem`, `PrivateNetwork`, etc.) —
   near-free, shares the host kernel, fine for trusted services. Cheap enough to turn
   on per-service as services get added, not a phase of its own.
2. **bubblewrap** — namespace-based, unprivileged, still shares the host kernel, no
   protection from kernel exploits.
3. **microvm.nix** — real KVM isolation, separate kernel per sandbox, virtiofs for
   store access. The right tier for anything untrusted — e.g. agent-executed code
   from the internet, once that's a real thing. Real precedent exists: someone
   already ran a coding agent inside microvm.nix on NixOS (Feb 2026 writeup).

All three probably end up wanted eventually, for different things — just not now.

## Secure boot

**lanzaboote** — proper Secure Boot for NixOS (Rust UEFI stub, plays nice with the
generations model, unlike naive UKI bundling which burns through ESP space fast).
Requires systemd-boot first. Pairs well with TPM-backed full-disk encryption. Later.
