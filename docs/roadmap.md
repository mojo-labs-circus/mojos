# Roadmap

## Phase 0 — Pre-install prep

Before touching real hardware.

- Back up everything: `~/.ssh`, `~/.gnupg`, browser profile, dotfiles, licence keys, wifi/VPN configs
- Verify backups by actually restoring files, not just confirming the copy finished
- Dry-run the partition layout, subvolumes, and GRUB setup in a throwaway VM

## Phase 1 — OS Foundation

Goal: a working, rollback-capable MojOS install. The finish line is not "it boots" — it's "rollback works, confirmed by hand under deliberately broken conditions."

- Partition layout: EFI + single btrfs partition
- Btrfs subvolumes: `@stable`, `@staging`, `@home_stable`, `@home_staging`, `@var_log`, `@var_cache_pacman_pkg`, `@snapshots`
- GRUB with two entries — one for `@stable`, one for `@staging`
- grub-btrfs (snapshot boot entries), snapper (snapshot triggers), etckeeper, pacman snapshot hook
- MojOS install script with TUI
- Stress-test rollback by hand — deliberately break things in staging, confirm clean recovery
- Practice the full staging → stable promotion flow manually
- Define the mojo-agent interface contract on paper — what MojOS exposes, what the agent is allowed to do

## Phase 2 — Rice & Dotfiles

Goal: MojOS looks and feels the way it should. The aesthetic is part of the product.

- Hyprland compositor config
- waybar, rofi, kitty — full desktop environment
- The MojOS visual identity: warm retro-futurism, aged paper, phosphor terminals
- Modes: work, coding, music
- Dotfiles repo set up and wired into the install script

## Phase 3 — mojo-agent Basic

Goal: agent running inside staging with minimal permissions. Validate the safety model works before expanding trust.

- Agent installed in `@staging` with read-only access first
- Snapshot before and after every agent action
- Manual staging → stable promotion workflow
- Agent can observe system state and report back

## Phase 4 — System Control

Goal: agent can manage the machine through the staging model.

- Agent can install packages, edit config, manage services
- Full privilege model implemented — defined set of allowed actions, gates on dangerous ones
- Agent validates its own changes before promoting
- Promotion ledger operational
- Trust ramp begins: low-risk categories start auto-promoting, novel/destructive never do

## Phase 5 — Computer Use

Goal: the Jarvis layer. Agent can see and drive the full desktop.

- Display access: pipewire screen capture, uinput injection
- Agent can observe and interact with any running application
- Self-generated skills reviewed before being trusted to run unattended

## Phase 6 — Polish & Scale

- GUI installer (replacing the TUI)
- Multi-machine: laptop → PC → server proxy architecture
- Graceful degradation when server is unreachable
- Inter-device auth treated with the same rigour as local confirmation gates
