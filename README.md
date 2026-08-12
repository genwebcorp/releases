# GenWeb App Releases

Public installers for downloadable GenWeb apps — the apps you can run without a
monorepo checkout.

## Install

```sh
curl -fsSL https://raw.githubusercontent.com/genwebcorp/releases/main/genweb | sh
```

That installs `genweb`, which installs and runs everything else:

```sh
genweb install inbox
genweb inbox
```

| Command | App | What it is |
| --- | --- | --- |
| `inbox` | `genweb-inbox` | Local TUI inbox with natural-language search over Gmail and Takeout archives |
| `cogent` | `cogentkit-cli` | Headless inspection and layered mutation for Cogent authoring packages |

Everything lands under `~/.genweb`, with each command in `~/.genweb/bin`, which is
added to your `PATH` in your shell startup files. Open a new shell afterwards, or run
the printed absolute path.

## Using `genweb`

```sh
genweb login                 # opens a browser only when it has to
genweb install inbox         # newest published version
genweb install inbox@1.2.3   # an exact version
genweb update inbox          # says so when already current
genweb list                  # what is installed on this machine
genweb inbox --help          # run an app; its arguments are its own
genweb logout                # revoke this machine's session
```

## Requirements

- Apple Silicon macOS, or `x86_64` Linux. Intel Macs are not currently supported; the
  installer will tell you rather than installing something that cannot run. Any Linux
  distribution works: the binaries are linked statically and need no particular glibc.
- A GenWeb account. The first thing the installer does is open a browser to verify
  one; nothing downloads until it has. **The script here is public, the software it
  fetches is not.**
- Nothing else — no GitHub CLI, no checkout. If your Node is older than an app needs,
  `genweb` fetches a verified copy of Node from `nodejs.org` into `~/.genweb/node`
  and uses that instead of touching your system install.

Everything downloaded is checksum-verified before it is unpacked, and a release is
only activated after it has been run once on your machine and behaved.

The apps themselves also expect GenWeb SSO and reach internal services at
`*.dev.genweb.io` at runtime, so they are useful to GenWeb accounts only.

## Retired per-command installers

Bookmarks that still curl a per-command path fail loudly and point here:

```sh
curl -fsSL https://raw.githubusercontent.com/genwebcorp/releases/main/inbox | sh
# prints the genweb bootstrap one-liner and exits non-zero
```

## Upgrading and rolling back

`genweb update inbox` takes the newest published version. Installing leaves the
previous release on disk, so a bad one is a symlink flip away from fixed:

```sh
ls  ~/.genweb/apps/genweb-inbox            # release ids, newest last
ln -sfn <previous-release-id> ~/.genweb/apps/genweb-inbox/current
```

To go back to a specific published version instead, name it:

```sh
genweb install inbox@1.2.3
```

An app that runs a background daemon keeps using its old copy until the daemon is
restarted. The manager says so rather than killing it for you.

## Interactive installs

The bootstrap one-liner pipes into `sh`, which consumes stdin, so it never prompts —
it prints the command you need and exits. If you would rather it be able to ask, use
the command-substitution form:

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/genwebcorp/releases/main/genweb)"
```

## Source of Truth

This repository is synced from the private GenWeb monorepo. `genweb` is generated
from `tools/scripts/genweb_bootstrap_install.sh`, each retired per-command path is a
tombstone from `tools/scripts/release_install_tombstone.sh` over the frozen command
list in `tools/scripts/release_tombstone_commands.txt`, and this README from
`tools/scripts/release_public_README.md`. Edit them there; changes land here
automatically after merge to `main`. Nothing in this repository should be edited
directly.
