# Rodecaster Duo - PipeWire virtual devices (Game, System, Chat, Music)

PipeWire virtual sinks and sources for the **Rodecaster Duo**, matching the Windows layout:

- **Outputs:** Game, System, Chat, Music  
  - Game, System, Music → Main Multitrack (pro-output-1, AUX0–AUX9)  
  - Chat → separate Chat device (pro-output-0)
- **Inputs:** Main Multitrack, Chat (pro-input-1, pro-input-0)

The package **auto-detects** your Rodecaster Duo.

---

## Install (Arch Linux / CachyOS)

From the AUR (e.g. with [yay](https://github.com/Jguer/yay)):

```bash
yay -S rodecaster-duo-pipewire
```

Or build from this repo:

```bash
git clone https://github.com/rewalo/rodecaster-duo-pipewire.git
cd rodecaster-duo-pipewire
makepkg -si
```

**With your Rodecaster Duo connected**, the package will automatically set the Pro Audio profile, generate the PipeWire config, and restart PipeWire. You should see **Game**, **System**, **Chat**, and **Music** (outputs) and **Main Multitrack** / **Chat** (inputs) right after install.

---

## If the Duo wasn’t connected during install

If you install without the device plugged in (or it wasn’t detected), run once:

```bash
rodecaster-duo-set-pro-audio
rodecaster-duo-pipewire-install
systemctl --user restart pipewire pipewire-pulse
```

Then choose **Game**, **System**, **Chat**, or **Music** as output, and **Main Multitrack** or **Chat** as input.

---

## Commands (package install)

| What | Command |
|------|--------|
| Set Pro Audio profile | `rodecaster-duo-set-pro-audio` |
| List / discover devices | `rodecaster-duo-pipewire-discover` |
| Install / regenerate config | `rodecaster-duo-pipewire-install` |
| Apply config | `systemctl --user restart pipewire pipewire-pulse` |

---

## Manual / script-only usage (no package)

If you run the scripts from a git clone instead of installing the package:

```bash
# Set Pro Audio (replace with your card name from pactl list cards short)
pactl set-card-profile alsa_card.usb-R__DE_RODECaster_Duo_XXXXX-00 pro-audio

cd /path/to/rodecaster-duo-pipewire
./install-rcp-duo-pipewire.sh
systemctl --user restart pipewire pipewire-pulse
```

Discovery:

```bash
./install-rcp-duo-pipewire.sh   # auto-detects and installs
# or
./install-rcp-duo-pipewire.sh -d   # discover only
./discover-rcp-devices.sh          # same
```

---

## Device renaming

Names (Game, System, Chat, Music, Main Multitrack, Chat) are set in the config templates as `node.description`. To rename devices:

- Edit the generated configs in `~/.config/pipewire/pipewire.conf.d/` and change the `node.description = "..."` values, or  
- Edit the templates in `/usr/share/rodecaster-duo-pipewire/` (when installed) and re-run `rodecaster-duo-pipewire-install`.

---

## Channel map (outputs on Main Multitrack)

| Output | AUX pair  |
|--------|-----------|
| System | AUX0 AUX1 |
| Game   | AUX2 AUX3 |
| Music  | AUX4 AUX5 |
| Chat   | pro-output-0 (separate device) |

---

## Where config is installed

- `~/.config/pipewire/pipewire.conf.d/99-rodecaster-duo-virtual-sinks.conf` (outputs)
- `~/.config/pipewire/pipewire.conf.d/99-rodecaster-duo-virtual-sources.conf` (inputs)

Loaded automatically at login.

---

## Troubleshooting

- **No multichannel Rode device found**  
  Set the Duo to **Pro Audio** first: `rodecaster-duo-set-pro-audio`, then run `rodecaster-duo-pipewire-install` again.

- **Chat output not working**  
  Chat uses **pro-output-0**. The installer detects it as the Rode playback node with the fewest channels. Ensure the Duo is in Pro Audio and re-run `rodecaster-duo-pipewire-install`.

- **Only stereo Rode in discovery**  
  Set the Duo to **Pro Audio**, then run `rodecaster-duo-pipewire-discover` again.

- **Different card name**  
  Run `pactl list cards short` and use your card name with `pactl set-card-profile <card-name> pro-audio`, or use `rodecaster-duo-set-pro-audio` which auto-detects Rode cards.

---

## License

MIT

---

## Publishing to the AUR

1. Create a GitHub repo and push this project.
2. Create a new AUR package (e.g. `rodecaster-duo-pipewire`) and clone the AUR repo:
   ```bash
   git clone ssh://aur@aur.archlinux.org/rodecaster-duo-pipewire.git
   cd rodecaster-duo-pipewire
   ```
3. Copy `PKGBUILD` and the six source files into the AUR repo. For a release-based source, change `PKGBUILD` to use:
   ```bash
   source=("https://github.com/rewalo/rodecaster-duo-pipewire/archive/v$pkgver.tar.gz")
   sha256sums=('...')  # run updpkgsums after uploading a release
   ```
   and in `package()` use `cd "$srcdir/rodecaster-duo-pipewire-$pkgver"` before the `install` commands.
4. As a **non-root** user, run `makepkg --printsrcinfo > .SRCINFO`, then commit and push to AUR.

Alternatively, users can install from this repo with `git clone ... && cd ... && makepkg -si` (as non-root).
