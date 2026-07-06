# Devlog

*Dated thinking for the OS layer specifically. Project-wide vision decisions still
live in the thinking repo's devlog — this one's for MojOS design and build reasoning.*

---

## 2026-07-06 — Nix installed on nomadbaker; flake anatomy taught, nothing written yet

Follow-up session. Circus/installer/model-selection tangents got parked into
`mojo/ideas.md` and `mojo-agent/devlog.md` rather than designed here — this
entry is just the MojOS-side ground actually covered.

**Nix (Determinate installer) is now installed on nomadbaker** (this laptop,
confirmed via `/etc/hostname` — `pearlybaker` is the desktop, for later).
Flakes enabled by default. Nothing installed to NixOS itself; this is Nix the
tool running on top of the existing Arch install, specifically so the flake can
be written and VM-tested without touching real hardware — same "VM before
metal" instinct as always, just clarifying that Nix-on-Arch is the dev loop, not
a detour from it.

**Decided: `nixos-unstable`, not a stable release channel.** Hyprland and
Hermes both move fast enough that a 6-month-stale stable channel would be
actively annoying. Normally that trades against stability, but the reversibility
architecture (generations/snapper/impermanence, decided yesterday) exists
specifically to make "this broke" cheap to undo — that safety net is what makes
unstable the low-risk choice here rather than the reckless one.

**Decided: VM build approach for iterating on the flake.** `nixos-rebuild
build-vm` (or the equivalent raw `nix build .#nixosConfigurations.<host>.config.
system.build.vm`) for the fast day-to-day loop; `build-vm-with-bootloader`
before calling anything actually "VM-proven," since plain `build-vm` skips the
real bootloader path and this project cares a lot about what happens at boot.
Keep the VM's disk image persistent across runs rather than wiping it each
time — proving impermanence works means rebooting the *same* disk repeatedly
and confirming root comes back blank while `/persist`/`/home` survive, which a
fresh-disk-every-run habit would never actually test.

**Taught, not yet written:** flake anatomy — `inputs` (pinned via `flake.lock`,
same role as a lockfile in any other ecosystem), `outputs` as a function
returning `nixosConfigurations.<host>`, `nixpkgs.lib.nixosSystem` taking a
module list, a NixOS module being nothing more than an attribute set of
options, `system.stateVersion` as a compatibility marker rather than a version
pin. No `flake.nix` exists on disk yet — deliberately. Clarke wants to write the
first pass himself next session, taught line by line, not handed a finished
flake.

Next: `flake.nix` + `hosts/nomadbaker/configuration.nix`, written by Clarke with
explanation as it happens, then the VM build to prove it boots.

---

## 2026-07-05 — Closing the reversibility questions; stable/staging confirmed dead

Follow-up session, same day, closing both open choices from the entry below.

**Decided: root wipe is tmpfs + the nix-community `impermanence` module**, not
btrfs blank-snapshot rollback — most prior art, least custom code, disk-vs-RAM is
a non-issue at this scale. Scope, for later reference: only `/` is wiped. `/home`,
`/nix/store`, and a `/persist` partition (machine-id, SSH host keys, saved wifi —
OS state that must survive a reboot but Nix doesn't declare) stay normal and
disk-backed. Point isn't saving RAM, it's a drift detector: anything not declared
in the flake or the persist list doesn't survive a reboot, so you find out
immediately if config secretly depended on undeclared state.

**Decided: snapshot trigger is per-agent-action, called from mojo-agent's
harness, not a timer and not the agent's own judgment.** A timer is disconnected
from real task boundaries; agent-judgment ("this seems risky, snapshot") puts the
safety net behind the one component that isn't fully trusted, and it can skip the
step in the same failure mode it's meant to catch. The call belongs in the
deterministic dispatcher between "agent picked an action" and "action runs" —
same principle as instrumenting logging at the call boundary instead of trusting
code to log itself. Btrfs snapshots are cheap CoW, so per-action is affordable.
MojOS's share is narrow: snapper installed/configured, mojo-agent's service user
allowed to call `snapper create` without full root. Which actions get wrapped,
and an async "should this promote" idea that came up alongside it, are
mojo-agent behavior — logged to that repo's own new devlog.md instead, as a
candidate for the first thing built *with* Hermes once it's running, not
something to hand-design now.

**Confirmed, not re-decided: the old `@stable`/`@staging` +
`@home_stable`/`@home_staging` split is fully replaced**, not partially. NixOS
generations already covered config (session 3); snapper-on-data already covered
home/files (session 1, below: one live copy, snapshot before each action, roll
back if it goes bad). None of the three replacement mechanisms are a
two-live-copies-with-promotion model. Real trade worth staying aware of: the old
model gave *isolation* (bad edits never touch live files until promoted);
snapshot-per-action gives *reversibility* (they do touch live files, briefly, but
roll back cleanly). Accepted for now, simpler, matches the actual goal from
session 1 — revisit only if reversibility-after-the-fact stops being enough.

**Docs process note:** considered whether decisions like these need an ADR
system as the repos grow. Checked the thinking repo — no such plan exists, and
its stated convention (`mojo/AGENTS.md`) is explicitly append-only devlog + flat
structure, no scaffolding until a topic earns its own file. Retrofitting ADRs
later from devlog entries is free if it's ever needed; building that machinery
now, at two files and zero code, would be the exact kind of premature structure
the project already rules out. Staying with devlog, marked with bold "Decided:"
leads on settled points so it stays scannable.

Next: the flake skeleton, for real this time — V0.1 step 1, smallest flake that
boots in a VM. First session writing actual Nix; teach concepts as they come up,
Clarke drives every structural decision.

---

## 2026-07-05 — Flake design session: baker as Rosetta stone, and the reversibility model

First real design conversation for the flake, before writing any of it.

Looked at baker (my current Arch install repo) side by side with what NixOS actually
gives you, and it mapped almost line for line: `packages/base.txt` is a shared
module, `packages/profile/{workstation,laptop}.txt` is just "the host" now instead of
a tag you branch on, `.baker-config` is plain NixOS options (hostname, timezone,
locale) with `nixos-rebuild switch` as the `baker-update` equivalent except atomic
and rollback-able for free. The one real gap: home-manager, which baker never had —
per-user config/dotfiles/packages as its own declared layer, distinct from system
config. Baker was, not unkindly, a hand-rolled and incomplete version of what Nix
does natively.

Decided: build concrete host modules first — `nomadbaker`, `pearlybaker`, and
whatever the third machine ends up called — not generic `laptop`/`workstation`
placeholders. The generic, give-it-to-the-open-source-community version (choose your
machines, choose your packages, join a circus) is real and wanted, but it's the same
mechanism at v0.1 scope (multi-host was already a day-one constraint), just without
building an installer/wizard layer that nothing needs yet. Make it work for me for
real, extract the redistributable pattern once there's a working config to extract
it from.

The bulk of the session was the reversibility/safety architecture — the actual
prerequisite for letting an agent have real system control without every mistake
being permanent. Landed on three separate mechanisms, each covering a different kind
of mutable state, not one unified thing:

- **NixOS generations** version the *system config*. Already solved, free, native —
  every `switch` is a commit, rollback is picking a previous generation.
- **Snapper snapshots on a persistent volume** version *actual data* (Documents,
  Projects, anything real). This is the one that can genuinely work the way I wanted
  — agent starts a task, snapshot taken as the branch point, task goes well and you
  just keep going, task goes badly and you roll back to that point. Whether that's
  periodic-only or has an agent-triggered on-demand hook for "snapshot before this
  specific risky thing" is still open.
- **Impermanence** (ephemeral root, wiped back to blank every boot, persistent paths
  explicitly declared) is the floor under both of the above — not a staging/promotion
  step, no decision involved. Anything not deliberately declared as config or data is
  scratch paper by default, whether the work on it was good or bad. Two real
  implementation options still open: tmpfs root (simple, RAM-backed) vs. btrfs
  blank-snapshot rollback (disk-backed, fits the subvolume thinking from session 2).

Net result: filesystem and config damage — the "agent wrecks my system or my files"
fear — is basically a solved problem architecturally, pending the two open
implementation choices above. What's left is not that.

What's left, and deliberately parked rather than designed tonight: irreversible
actions with *external* side effects (a sent message, a remote push, a spent API
call — none of that is undone by rolling back a local filesystem, no matter how good
the snapshot discipline is), and the live execution sandbox around the running agent
process (what it can reach *while it's running* — network egress, capabilities,
resource limits — independent of whether its writes are reversible after the fact).

The sharp bit worth remembering: those two remaining problems aren't actually
separate. Unrestricted local reads (SSH keys, secrets, personal files) are fine —
genuinely fine, that's the whole point of going local-first instead of handing
everything to someone else's cloud — specifically *because* there's no exfiltration
path if network egress is locked down. So the single highest-leverage piece of the
sandbox problem, whenever it gets built, is the network boundary. Everything else
(privilege escalation, resource limits) matters too, but that one carries more
weight than it looks like on first pass.

Nothing implemented yet — this was all design. Next: pick the two open
implementation choices (root wipe mechanism, snapshot trigger design), then start
the actual flake skeleton.
