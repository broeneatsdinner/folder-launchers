# Folder Launchers

Spotlight-launchable Finder folder aliases for macOS.

The tracked template is `launchers.example.md`. Copy it to `launchers.md` for local use.

Running `./build` generates:

- user-facing Spotlight launchers in `apps/*.app`
- a shared Finder runner at `runner/Folder Launcher Runner.app`

The `apps/` directory is symlinked into:

```text
~/Applications/Folder Launchers
```

This allows Spotlight to find launchers by their friendly names. The `runner/` directory is infrastructure and should not be symlinked into Applications.

Generated launcher apps are dumb shell-backed `.app` bundles. They delegate to the shared runner, which opens the configured folder in Finder:

- in a new Finder window if Finder has no windows
- in a new Finder tab if Finder already has a window
- with the resulting Finder window or tab switched to List View

The `apps/` and `runner/` directories are generated artifacts and are ignored by git.

Assigned launcher icons live in `assets/icons/assigned/`. To assign an icon to a launcher, name the `.icns` file so it begins with the launcher name, such as `Projects Computer RGB Green.icns`. Running `./build` reapplies assigned icons when regenerating apps.

## Usage

Copy `launchers.example.md` to `launchers.md`, edit the local paths, then run:

```bash
./build
```

To list the current launchers:

```bash
list-launchers
```

macOS may request Accessibility permission for `bash`. The generated runner is shell-backed and uses Finder/System Events automation to create Finder tabs and switch to List View.

## Launcher definitions

Launcher definitions are copied from the tracked `launchers.example.md` template into the local `launchers.md` file. `launchers.md` is ignored by git because it contains machine-local folder paths.

The launcher table intentionally does not support literal pipe characters inside names or paths. This keeps the parser simple, predictable, and easy to audit. As a convention, do not use `|` in folder or launcher names.
