# MojOS

Linux is the most powerful, private, and customisable operating system in the world.
Most people never get to use it — because using it well means learning a discipline
most people have no reason to learn. AI agents change that.

MojOS is a NixOS-based system built around a local AI agent
([mojo-agent](https://github.com/mojo-labs-circus/mojo-agent)) from the ground up.
The agent is the primary interface, and the OS is designed to make that safe: the
entire system is declared in one flake, every change lands as a NixOS generation —
staged, validated, always safe to undo. Full system control that can't quietly break
your machine.

It's also a workshop with moods: switchable modes that change how the system looks,
behaves, and how much of it shows — a dense coding mode, a warm creative mode, a
plain-looking mode for when other people are around.

## Status

**v0.1 in progress** — nothing installable yet. The current milestone (flake, modes,
VM-proven, then daily driver) is in [V0.1.md](V0.1.md).

The vision and philosophy behind this live in the
[mojo](https://github.com/mojo-labs-circus/mojo) repo — that's the thinking; this
repo is part of the proof.

## License

[AGPL-3.0](LICENSE)
