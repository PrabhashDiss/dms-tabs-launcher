# Browser Tabs Launcher for DMS

A [DankMaterialShell](https://github.com/AvengeMedia/DankMaterialShell) launcher plugin that lists every open browser tab and lets you jump to one from the launcher. Tabs are sourced via [tabctl](https://github.com/slastra/tabctl).

![Screenshot](screenshot.png)

## Features

- Lists open tabs across every browser tabctl is connected to
- Groups tabs by browser profile (`firefox`, `firefox2`, …) until you type a query
- Filters by title, URL, or profile as you type; multiple profiles also appear as launcher categories
- Activates the selected tab (focuses both tab and browser window)
- Website favicons from tabctl's JSON output, loaded asynchronously. Missing, unsupported, or failed images keep the browser icon (Brave, Brave Origin, Chrome, Chromium, Firefox, LibreWolf, Zen, Helium, Vivaldi, Opera).
- Favicons use the browser-provided HTTP(S) URL or embedded image data directly, without a third-party favicon service. Internal browser URLs and other unsupported schemes keep the browser icon.
- Context menu: copy URL, close tab, refresh tab list
- Always-active mode (skip trigger keyword)
- Cached tab list, refreshed in the background when you query the launcher and the cache is older than 5s; nothing runs while the launcher is idle

## Installing tabctl

The plugin shells out to the `tabctl` CLI, which talks to a browser extension via a native-messaging host and D-Bus. All three pieces must be installed for tabs to show up.

1. **Install the `tabctl` CLI** (pick one):
   ```bash
   yay -S tabctl              # Arch Linux (AUR)
   paru -S tabctl             # alternative AUR helper
   ```
   Or from source:
   ```bash
   git clone https://github.com/slastra/tabctl.git
   cd tabctl
   make build
   ```
2. **Install the native-messaging host manifests**:
   ```bash
   tabctl install             # AUR / on PATH
   ./build/tabctl install     # from a source checkout
   ```
3. **Install the browser extension** in every browser you want indexed:
   - Firefox / Zen: <https://addons.mozilla.org/en-US/firefox/addon/tabctl1/>
   - Chrome / Chromium / Brave / Brave Origin / Helium: <https://chromewebstore.google.com/detail/tabctl/baomblllgemcgbignhpbipgiofmjdhpn>
4. **Restart each browser** and verify with:
   ```bash
   tabctl status   # should report OK for each connected browser
   tabctl list     # should print TSV of open tabs
   ```

If `tabctl status` reports no browsers, the extension is missing/disabled or the native-messaging manifest is missing — re-run `tabctl install`, restart the browser, and check `busctl --user list | grep -i tabctl`.

## Installing the plugin

This fork is maintained at <https://github.com/PrabhashDiss/dms-tabs-launcher>.
Copy the plugin to your DMS plugins directory:

```bash
mkdir -p ~/.config/DankMaterialShell/plugins/tabsLauncher
cp TabsLauncher.qml TabsLauncherSettings.qml plugin.json \
  ~/.config/DankMaterialShell/plugins/tabsLauncher/
```

Restart DMS (`dms restart`) to discover the new plugin.

The [dotfiles mise configuration](https://github.com/PrabhashDiss/dotfiles/blob/main/.config/mise/config.toml) pins this fork to an exact commit. Its postinstall hook links `~/.config/DankMaterialShell/plugins/tabsLauncher` to mise's installed source and records the fork revision in `plugins.lock.json`. It preserves `plugin_settings.json` and moves an existing plugin checkout to `~/.config/DankMaterialShell/tabsLauncher.before-mise`. Run `mise install` after changing the pin, then restart DMS (`dms restart`) to clear cached plugin components. Manage this installation through mise, not the DMS plugin updater.

## Usage

Type `\tab` in the DMS launcher followed by your search query, or leave the query empty to see every open tab.

## Settings

Configure via DMS plugin settings:

| Setting | Description | Default |
|---|---|---|
| Enable Plugin | Toggle the plugin on/off | on |
| tabctl Path | Path to the `tabctl` executable | `tabctl` (PATH) |
| Always Active | Show results without a trigger prefix | off |
| Trigger | Prefix to list open tabs | `tab` |

## Requirements

- DankMaterialShell >= 1.4.0
- Quickshell
- [tabctl](https://github.com/slastra/tabctl) 2.3.1 or newer, with `list --format json` and `favIconUrl` support. See [Installing tabctl](#installing-tabctl) above. The plugin auto-discovers `tabctl` via the user's login shell (`$SHELL -lc`) so installs to `~/.local/bin` work without further configuration.

## License

MIT
