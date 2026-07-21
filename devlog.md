# Devlog

*Dated thinking for the OS layer specifically. Project-wide vision decisions still
live in the thinking repo's devlog — this one's for MojOS design and build reasoning.*

---

## 2026-07-21 — Cutting ties with Mojo/Paredros for real, repo shape settled, modes decided

Long discussion session, no code. Five parallel research forks (flake architecture,
NixOS/Hyprland ricing ecosystem, advanced NixOS capabilities, general Linux community
trends, what actually makes a rice good) plus a look at baker (the old Arch install
repo this replaces) and at how other personal NixOS repos structure themselves.

**Decided: MojOS doesn't mention Mojo/Paredros anywhere, not even in passing.** The
old interoperability-standard project renamed itself Paredros and moved on; MojOS
doesn't need it to exist to be worth building. home-manager, impermanence, secrets,
reversibility — everything that makes MojOS good is wanted regardless of whether a
future AI counterpart ever gets integrated. README/AGENTS.md each keep exactly one
forward-looking line acknowledging that possibility, unnamed and undepended-on.

**Decided: repo shape.** README.md is pure description, no status section —
structure does the explaining, matching what well-regarded personal Nix repos
actually do (mitchellh/nixos-config, totoroot/dotfiles, San7o/nixos-dotfiles).
AGENTS.md stays short and stable per the real agents.md convention (files over ~150
lines see diminishing returns and higher inference cost without better agent
behavior) — dropped its old "current stage" section entirely, since volatile status
doesn't belong in a doc meant to stay put. **No ROADMAP.md, no replacement for
V0.1.md** — this repo's own devlog convention already ends every entry with "Next:",
which *is* the roadmap, chronologically, without a parallel artifact to maintain.
V0.1.md deleted outright. New `ideas/` directory: one file per topic (modes, ricing,
flake-structure, secrets, fleet, sandboxing, misc), holds the unordered someday-stuff,
shrinks as things get built rather than "graduating" into another doc.

**Decided: flake structure.** Plain `nixosSystem`, no flake-parts (that earns its
keep for multi-platform or reusable-library flakes, neither applies to a personal
single-arch fleet). `hosts/<hostname>/` directories, added when a machine actually
exists.

**Decided: sops-nix over agenix, needed immediately, not deferred.** Even single-host
daily driving needs somewhere for SSH keys/wifi/tokens that isn't plaintext in git.
sops-nix's bundled-secrets-per-service model avoids a migration once the fleet
exists; the one extra concept it adds over agenix (a `.sops.yaml`) isn't a real
beginner blocker.

**Decided: ghost boots aren't a separate mechanism.** `nixos-generators` builds a
bootable image straight from a `nixosConfigurations.<host>` entry — a ghost host is
just another flake output, built as an ISO instead of installed to disk. Same
`disko` + `nixos-anywhere` combo covers PC install, server install, and ghost
re-provisioning.

**Decided: fleet deploy model, for when it matters.** Push (colmena) for the laptop
and one-shot ghosts; pull/GitOps (comin) for always-on hosts that should self-heal.
Mesh networking (tool TBD — deliberately not carrying over baker's Tailscale/
circus-tent tailnet just because it existed before) doesn't mean anything until a
second host exists, so it isn't its own phase — it attaches to whichever host is #2.

**Decided: modes change behavior first, look follows.** Three for now: regular/
classic (MojOS being itself — ships first, no switching mechanism needed for it to
exist), a second mode for when other people are watching the screen (not boring,
just restrained on nerd-signaling — no name settled, "muggle mode" proposed and
rejected), and coding (dialed in, terminal-heavy, fixed app set on entry). Session
"carries on from last time" splits into fixed declarative launch (in scope, every
mode gets one) vs true session resume (real runtime state, fights impermanence,
parked in ideas/modes.md). Per-project coding mode also parked. None of this blocks
the daily-driver milestone — regular mode alone is enough to replace Arch.

**Confirmed, not re-decided:** the reversibility trio (generations/snapper/
impermanence) from the 2026-07-05 entries below stands untouched.

**Hardware reality, not a design decision:** laptop-only for about the next two
months. Then back at uni, a daily-driver-grade gaming PC and a second, lesser PC
being repurposed into an always-on headless server both arrive at the same moment —
so "PC then server" was never actually an architectural choice, it's one physical
event. A dedicated "real" server is a further-out someday thought (noted in
ideas/fleet.md), not planned.

Git remote moved to `git@github.com:Dequavis-Fitzgerald-III/mojos.git`.

Next: the actual flake skeleton — `flake.nix` + `hosts/nomadbaker/configuration.nix`,
written by Clarke with explanation as it happens. sops-nix setup happens alongside
it, not after — secrets are day-one scope now, not a later add-on.

---

## 2026-07-21 — Bootstrap rule scoped, migration settled, modules-vs-flat left open

Same day, closing out after the commit above.

**Decided: VM-before-metal reworded as a bootstrap-phase rule, not a permanent one.**
Once MojOS is running on real hardware, the reversibility architecture (generations +
snapper + impermanence) is the safety net for further changes, not a VM step —
changes get made and switched from within MojOS directly at that point. AGENTS.md
updated.

**Decided: no bulk `~/` git dump for the Arch→MojOS migration.** Real risk in that
plan — SSH keys, browser profiles, shell history that's very plausibly got a pasted
secret in it somewhere, and git history doesn't actually forget just because a file
gets deleted later, private repo or not. Actual plan: a 64GB USB, Projects and
Documents only. Dotfiles/SSH keys/everything else gets rebuilt fresh per host anyway
— matches the existing convention (baker) of generating a new keypair per machine
rather than migrating an old one.

**Clarified, not re-decided:** home directory content isn't git-backed under this
design at all — it's Snapper-snapshotted on a persistent volume, a different and
better-suited mechanism than git for arbitrary/large/binary data (instant CoW
snapshots vs. git's commit overhead). Only the flake/config is git-backed. That
split is *why* Snapper exists as its own mechanism, decided back on 2026-07-05.

**flake-parts, re-covered:** still not needed now — single architecture, personal
fleet, not a published library, and it showed up in none of the reference repos
surveyed. Flagged as worth reconsidering specifically if ghost boots ever run on
non-x86_64 borrowed hardware (an ARM laptop, an SBC) — that's a real
multi-system-architecture problem, distinct from the modules question below.

**Left open, not resolved:** whether to split `modules/` (desktop/security/services/
etc.) from the very first host — applying SOLID and "best practices from day one" —
versus starting flat with one host config and extracting shared modules only once a
second host actually needs them. Made the case that SOLID doesn't transfer cleanly
from OOP to a one-person Nix config, and that the reference repos surveyed back
"flat first" (maxclax has no modules/ split at all; totoroot's concern-based split is
years-mature, not day-one). Clarke isn't convinced. Not forcing a resolution in the
abstract — genuinely open, revisit once there's real code to argue about instead of
a hypothetical.

**New, does matter regardless of how the above resolves:** future hosts include
ephemeral *profiles* (parameterized templates for different kinds of throwaway
system), not just named machines — a real requirement that changes `hosts/`'s
eventual shape, not speculative. Noted in ideas/fleet.md.

Next: actually start the flake — `flake.nix` + `hosts/nomadbaker/`, written by Clarke
with explanation as it happens. The modules-vs-flat question gets settled by however
that first file actually ends up structured, not argued to a conclusion beforehand.

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
