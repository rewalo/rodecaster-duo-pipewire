# rodecaster-duo-pipewire

PipeWire virtual devices for the **Rodecaster Duo**, matching the Windows layout.

**Main Multitrack** = RODECaster Duo Pro 1 (USB 1).

- **Outputs:** Game, System, Chat, Music
- **Inputs:** Main Multitrack (Pro 1), Chat (Pro 0)

Connect the Duo, install, done

---

## Install (Arch / CachyOS)

```bash
yay -S rodecaster-duo-pipewire
```

Or from source:

```bash
git clone https://github.com/rewalo/rodecaster-duo-pipewire.git
cd rodecaster-duo-pipewire
makepkg -si
```

With the Duo connected, the installer sets Pro Audio, generates config, restarts PipeWire. You get Game / System / Chat / Music (outputs) and Main Multitrack / Chat (inputs).

**Duo not connected during install?** Run:

```bash
rodecaster-duo-set-pro-audio
rodecaster-duo-pipewire-install
```

---

## Commands

| Command | Purpose |
|--------|---------|
| `rodecaster-duo-set-pro-audio` | Set Pro Audio profile |
| `rodecaster-duo-pipewire-discover` | List detected Rode devices |
| `rodecaster-duo-pipewire-install` | Regenerate config (auto-detects) |
| `systemctl --user restart pipewire pipewire-pulse` | Apply config changes |

**Important:** Run `rodecaster-duo-pipewire-install` without arguments. It finds the right devices automatically. Passing placeholder names like `TARGET_OBJECT` will break routing (everything ends up on Chat).

---

## Manual install (no package)

```bash
pactl set-card-profile alsa_card.usb-R__DE_RODECaster_Duo_XXXXX-00 pro-audio
./install-rcp-duo-pipewire.sh
systemctl --user restart pipewire pipewire-pulse
```

Replace the card name with yours (`pactl list cards short`).

---

## Channel map

| Output | Hardware |
|--------|----------|
| System | AUX0 AUX1 (Pro 1) |
| Game | AUX2 AUX3 (Pro 1) |
| Music | AUX4 AUX5 (Pro 1) |
| Chat | pro-output-0 (separate) |

---

## Config files

- `~/.config/pipewire/pipewire.conf.d/99-rodecaster-duo-virtual-sinks.conf`
- `~/.config/pipewire/pipewire.conf.d/99-rodecaster-duo-virtual-sources.conf`
- `~/.config/rodecaster-duo-pipewire/state.conf` - saved default sink for uninstall

Default sink is set to System so volume controls work.

---

## Renaming

Edit `node.description` in the generated configs under `~/.config/pipewire/pipewire.conf.d/`, then restart PipeWire.

---

## Troubleshooting

**No multichannel device found** - Set Pro Audio first: `rodecaster-duo-set-pro-audio`, then run `rodecaster-duo-pipewire-install` again.

**Chat not working** - Chat uses pro-output-0. Re-run install with the Duo in Pro Audio.

**All audio goes to Chat** - Config has placeholder names instead of real device names. Run `rodecaster-duo-pipewire-install` with no args (Duo connected, Pro Audio) to regenerate.

**Different card name** - `pactl list cards short` to find it, then `pactl set-card-profile <card-name> pro-audio`.

---

MIT
