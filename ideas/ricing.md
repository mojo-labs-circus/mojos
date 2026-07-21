# Ricing

Wishlist, not commitments — shrinks as things get built, doesn't move anywhere else.

## Backbone

**Stylix** — one base16 palette + wallpaper + font declared once, propagates to every
app with a Stylix target (terminal, GTK/Qt, Hyprland, notifications). Composes with
home-manager. Anything without a Stylix target still needs hand-theming.

## Modes vs wallust — resolved

`wallust` generates a colorscheme from whatever wallpaper is set, at runtime — that's
imperative, the opposite instinct from "everything reproducible from the flake."
Resolved: modes are declarative Stylix-variable swaps (decided, see modes.md).
`wallust` stays an optional side toy outside the formal mode system if it's ever
wanted — not wired into modes.

## The one real design principle that surfaced

Cohesion beats flash. One consistent logic (font, spacing, palette *logic*, not
necessarily one literal palette) applied everywhere reads as "designed." Minimalism
and maximalism are both fine — the actual failure mode is inconsistency, not a
specific point on that spectrum. Applies directly to the retro-futurism/phosphor
aesthetic already chosen: the risk isn't picking the wrong look, it's applying it to
the terminal and forgetting about it everywhere else.

## Aesthetic direction (already established, regular/classic mode's identity)

Warm retro-futurism, aged paper, phosphor terminal look. No existing project matches
this combination — genuinely original composition, not something to find and copy.

## Widget/launcher/notification options

Past what's already chosen (Hyprland, waybar, kitty, rofi-wayland, pipewire):

- Widgets: AGS/Astal — current standard for custom Hyprland shells, heavier than
  waybar, far more flexible.
- Launchers: walker, anyrun — newer, more Wayland-native than rofi-wayland.
- Notifications: mako — lightweight, has a Stylix module.
- Lock/idle: hyprlock/hypridle — Hyprland-native, tighter integration than
  swaylock/swayidle.
- catppuccin-nix — drop-in palette pack across many of the same targets Stylix covers.

None of these are decided — options to reach for if/when the current stack falls
short.
