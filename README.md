# Folder Launchers

Folder Launchers is a macOS workflow-engineering tool for generating Spotlight-searchable Finder launchers from a local Markdown table.

It turns frequently used project, reference, and working folders into small `.app` bundles that behave like native launcher targets. The result is a maintainable local automation pattern: folder access is named, reproducible, searchable, and integrated with Finder instead of buried in ad hoc shortcuts or machine-specific scripts.

## Why this exists

Local working systems accumulate friction. Important folders move into deep paths, project names become hard to remember, and repeated navigation steals attention from the work itself.

This repository treats folder access as a small systems-integration problem. It keeps private machine paths in a local file, generates stable macOS launcher bundles from that source of truth, and lets Spotlight become the human interface.

The goal is not to replace Finder. The goal is to reduce cognitive load around repeated navigation while keeping the underlying tooling plain, inspectable, and easy to rebuild.

## What it does

Running `./build` generates:

- user-facing Spotlight launchers in `apps/*.app`
- a shared Finder runner at `runner/Folder Launcher Runner.app`

Each generated launcher delegates to the shared runner. The runner opens the configured folder in Finder:

- in a new Finder window if Finder has no windows
- in a new Finder tab if Finder already has a window
- with the resulting Finder window or tab switched to List View

Assigned launcher icons live in `assets/icons/assigned/` and are reapplied when launchers are regenerated.

## How it works

The tracked launcher template is `launchers.example.md`. Users copy it to `launchers.md` and edit the local folder paths.

`launchers.md` is ignored by git because it contains machine-local absolute paths. This keeps the public repository reusable while allowing each operator to maintain their own launcher map.

The `apps/` and `runner/` directories are generated artifacts and are ignored by git. `apps/` contains the user-facing launcher apps. `runner/` contains shared infrastructure used by those apps.

For Spotlight integration, symlink `apps/` into:

```text
~/Applications/Folder Launchers
```

Only `apps/` should be symlinked into Applications. The `runner/` directory is infrastructure and should not be symlinked.

## Usage

Copy the tracked template:

```bash
cp launchers.example.md launchers.md
```

Edit `launchers.md` for the local machine, then generate the launcher apps:

```bash
./build
```

Create or update the Applications symlink so Spotlight can index the generated launchers:

```bash
mkdir -p "$HOME/Applications"
ln -sfn "$(pwd)/apps" "$HOME/Applications/Folder Launchers"
```

To list the configured launchers:

```bash
list-launchers
```

## Launcher definitions

Launcher definitions use a simple Markdown table copied from `launchers.example.md` into `launchers.md`:

```markdown
| Name | Path |
|---|---|
| Projects | /Users/example/Projects |
```

The `Name` becomes the friendly Spotlight-visible launcher name. The `Path` is the absolute folder path opened by Finder.

Literal pipe characters are not supported inside launcher names or paths. This keeps the parser simple, predictable, and easy to audit. As a convention, do not use `|` in folder or launcher names.

## Icons

Place assigned launcher icons in:

```text
assets/icons/assigned/
```

To assign an icon to a launcher, name the `.icns` file so it begins with the launcher name. For example, this icon can match a launcher named `Projects`:

```text
Projects Computer RGB Green.icns
```

Running `./build` copies matching assigned icons into the generated app bundles.

## macOS permissions

macOS may request Accessibility permission for `bash`. The generated runner is shell-backed and uses Finder/System Events automation to create Finder tabs and switch Finder to List View.

If Finder opens the folder but tab creation or view switching does not work, check the macOS Accessibility permission prompt and System Settings permissions for `bash`.

## Design goals

- Keep launcher definitions reproducible and reviewable.
- Keep private local paths out of the public repository.
- Integrate with Spotlight and Finder instead of creating a separate launcher UI.
- Make repeated folder navigation fast without hiding the mechanism.
- Keep generated app bundles disposable and rebuildable.
- Separate user-facing launchers from shared runner infrastructure.
- Use simple local tooling that can be inspected, repaired, and maintained.
