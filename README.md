
# gn-display-tools

Two tiny utilities to recover GNOME when displays misbehave:

- **`gn-fix`** — repair GNOME login loops and socket/permission issues; collect logs; toggle Wayland/Xorg.
- **`dp-recover`** — recover a lost DisplayPort session without logging out; optionally soft-rebind GPU driver.

## Quick install (manual .deb)

Download the latest `.deb` from the [Releases](../../releases) page, then:

```bash
sudo dpkg -i gn-display-tools_0.2.1_all.deb
```

## Quick reference

```bash
# GNOME repair toolkit
gn-fix repair           # clean locks/sockets, fix /tmp/.X11-unix perms, restart GDM
gn-fix guard-install    # enforce correct /tmp/.X11-unix perms on every boot
gn-fix status           # show state and recent GDM/gnome-shell errors
gn-fix collect          # save logs to ~/wayland_crash_*.txt
gn-fix extensions-off   # disable all user extensions & clear cache
gn-fix extensions-on    # re-enable user extensions
gn-fix gpu-info         # summarize GPU/driver/DRM info

# DisplayPort recovery (no logout)
dp-recover              # hotplug + Mutter refresh
dp-recover --modprobe   # soft driver rebind (nvidia/amdgpu/i915)
dp-recover --force-gdm  # last resort: restart GDM (ends session)
dp-recover --help       # options
```

## Add an APT repo (GitHub Pages) — optional

1. **Generate a GPG key** on your machine (or a CI-only key):  
   ```bash
   gpg --quick-generate-key "gn-display-tools <you@example.com>" rsa4096 sign 1y
   gpg --armor --export gn-display-tools > public.key
   gpg --armor --export-secret-keys gn-display-tools > private.key
   ```
2. **Create repo structure** (CI will publish it to GitHub Pages):
   - Create repository settings → Pages → Source: `gh-pages` branch / root.
3. **Add GitHub Secrets**:  
   - `GPG_PRIVATE_KEY` → contents of `private.key`  
   - `GPG_PASSPHRASE` → your key passphrase (or empty if none)
4. **Tag a release** (e.g. `v0.2.1`). CI will:
   - Upload the `.deb` to the Release
   - Build `Packages`, `Release`, `InRelease` for `./apt/`
   - Push generated APT repo to `gh-pages`

### Client-side APT setup

```bash
# On machines that should install via APT:
curl -fsSL https://<your-username>.github.io/gn-display-tools/public.key | sudo gpg --dearmor -o /usr/share/keyrings/gn-display-tools.gpg
echo "deb [signed-by=/usr/share/keyrings/gn-display-tools.gpg] https://<your-username>.github.io/gn-display-tools apt main" | sudo tee /etc/apt/sources.list.d/gn-display-tools.list
sudo apt update
sudo apt install gn-display-tools
```

## Development

- Scripts live in `/usr/local/bin` after install.
- The `deb/` folder contains a prebuilt `.deb`; CI can replace it on new tags.
