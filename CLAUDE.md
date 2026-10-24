# herdr-config

herdr config shared by two machines: a macOS laptop and a Linux (Omarchy) PC.

## What is tracked

- `config.toml`: herdr settings and keybindings, the same on both machines.
- `plugins/config/herdr-plugin-workspace-manager/config.yml`: workspace-manager layouts.
- `.gitignore`: ignores everything else. herdr writes runtime state (sockets, logs, `session.json`, plugin checkouts) into the same folder.

A new file needs a `!path` line in `.gitignore` before git can see it.

## Never track plugins.json

herdr generates `plugins.json` when a plugin is installed. It holds absolute paths (`/Users/gillesmajor/...` on macOS, `/home/gilozoaire/...` on Linux), so a shared copy breaks the other machine's plugins. It is gitignored and must stay that way.

The file is still required on each machine: it is herdr's list of installed plugins. Deleting it uninstalls the workspace manager. Do not delete it to clean up the repo, and do not `git clean -x` here.

### Machine where plugins.json was once tracked (the Mac)

Commit 4d9c593 removed `plugins.json` from git. On a checkout that still tracks it, `git pull` deletes the local file. Back it up first:

```sh
cp plugins.json /tmp/plugins.json.bak
git pull
cp /tmp/plugins.json.bak plugins.json   # now ignored, stays local
```

If it is already gone, reinstall the plugin through herdr (source: github `razajamil/herdr-plugin-workspace-manager`). That regenerates the file.

## How each machine links this repo

- **macOS:** `~/.config/herdr` is a checkout of this repo.
- **Linux:** the repo lives at `~/dev/omarchy_apps/herdr-config`, and only these paths in `~/.config/herdr` are symlinks to it:
  - `config.toml`
  - `plugins/config/herdr-plugin-workspace-manager/`

  `plugins.json` and the plugin checkout under `plugins/github/` are real local files.

## Checking a change

- Layouts: from the plugin checkout, run
  `HERDR_PLUGIN_CONFIG_DIR=~/.config/herdr/plugins/config/herdr-plugin-workspace-manager sh bin/herdr-workspace-manager validate`
- `config.toml`: reload in herdr with `prefix+q`. Keys are shared, so check that a new binding works on both macOS and Linux. On macOS, Option only works as `alt` if the terminal sends it as Meta.
