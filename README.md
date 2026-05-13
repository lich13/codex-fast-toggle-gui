# Codex Fast Toggle GUI

Standalone one-click GUI toggles for Codex Desktop Fast Mode on macOS and Windows.

## Files

- `Codex Fast Toggle.app` - macOS double-click app.
- `CodexFastToggle.hta` - Windows double-click HTA GUI.
- `assets/` - source icon assets.

## macOS

Double-click `Codex Fast Toggle.app`.

The app toggles Fast Mode, updates both Codex state files, reopens Codex Desktop, and shows a native confirmation dialog. If macOS blocks the app because it was downloaded from the internet, right-click it and choose Open once.

## Windows

Double-click `CodexFastToggle.hta`.

The GUI has buttons for refresh, enable, and disable. It runs a temporary PowerShell runtime internally so the HTA does not need `ADODB.Stream` or external scripts. The log shows the main and backup tier values after refresh/apply, for example:

```text
main=<missing>; bak=<missing>; home=C:\Users\you\.codex
```

## State Files

Both tools edit:

- `.codex-global-state.json`
- `.codex-global-state.json.bak`

Status display uses `.codex-global-state.json` as the source of truth. The `.bak` file is still synchronized during writes because Codex may preserve or consult it across app launches.

Fast mode is represented by `default-service-tier: "fast"`. Turning it off removes the explicit service-tier override and the related service-tier flags, matching Codex Desktop's normal non-fast state where requests are sent without `service_tier`. This avoids forcing `service_tier: flex`, which some relays or upstreams reject with Cloudflare 502 responses.

Backups are written to:

- `~/.codex/fast-toggle-backups` on macOS
- `%USERPROFILE%\.codex\fast-toggle-backups` on Windows

## Safety Notes

- The tools only patch Codex's user state files under the Codex home directory.
- Existing state files are backed up before each write.
- The Windows implementation edits only top-level fields under `electron-persisted-atom-state`, so old prompt history or error text cannot create false Fast status.
