# Fleet — provisioning, deploy, networking

Wishlist, not commitments — shrinks as things get built, doesn't move anywhere else.

## Hardware reality (not a design decision, just what's actually true)

Laptop-only for about the next two months. Then back at uni: a beastly gaming PC
(daily-driver-grade) and a second, lesser-but-good PC being repurposed into an
always-on headless server — both exist at the same moment, so "PC then server" was
never an architectural choice, it's one physical event. A dedicated "real" server is
a further-out someday thought (once older, more into running local AI, hopefully
alongside Paredros if that ever exists) — noted here, not planned.

## Provisioning — one mechanism, not three

`disko` (declarative partitioning) + `nixos-anywhere` (SSH into any live-booted
target, kexec, run disko, install the flake, reboot) + `nixos-generators` (build a
bootable image straight from a `nixosConfigurations.<host>` entry) cover PC install,
server install, *and* ghost-boot image generation with the same mechanism. A ghost
host is just another flake output, built as an ISO instead of installed to disk — not
a separate thing to design.

## Deploy model — push vs pull, by host type

**colmena** (push) for the laptop and one-shot ghosts — nothing daemon-side needed,
fits hosts that aren't always reachable or are disposable anyway. **comin** (pull,
GitOps — machines poll the repo and converge themselves) for always-on hosts that
should self-heal without a manual push — the server, maybe the gaming PC once it's
proven out.

## Mesh networking

Starting fresh — not carrying over baker's Tailscale/`circus-tent` tailnet just
because it's what was there before. Tool choice still open. Doesn't mean anything
until a second host exists, so it isn't its own phase — it attaches to whichever host
becomes #2.

## Once there are 3+ hosts

- **attic** (self-hosted Nix binary cache) — one machine builds, everyone else pulls
  the binary instead of rebuilding.
- **restic** — has a native NixOS module (`services.restic.backups`), pairs naturally
  with the server as an off-machine backup target.
