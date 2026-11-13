# PacShelf

Custom Arch Linux packages, published at
[`pacshelf.moreyet.com`](https://pacshelf.moreyet.com).

## Setup

Run this once to trust the PacShelf signing key and add the repository to
Pacman:

```sh
curl -fsSL --proto '=https' -o /tmp/pacshelf.asc \
  https://pacshelf.moreyet.com/pacshelf.asc &&
sudo pacman-key --init &&
sudo pacman-key --add /tmp/pacshelf.asc &&
sudo pacman-key --lsign-key 9243E163FD44CCF22FF9C29E6F582689B321A703 &&
rm -f /tmp/pacshelf.asc &&
printf '%s\n' '' '[pacshelf]' \
  'SigLevel = PackageRequired DatabaseRequired TrustedOnly' \
  'Server = https://pacshelf.moreyet.com/$arch' |
  sudo tee -a /etc/pacman.conf >/dev/null
```

The full signing-key fingerprint is
`9243 E163 FD44 CCF2 2FF9 C29E 6F58 2689 B321 A703`.

Install a package with a full system upgrade:

```sh
sudo pacman -Syu PACKAGE_NAME
```

## Development

`packages/` contains package definitions, `.SRCINFO` metadata, and
`.nvchecker.toml` update sources. `scripts/` contains the build, update, release,
and cleanup commands. `.github/workflows/` runs updates and validation.

Install the development tools on Arch Linux:

```sh
sudo pacman -S --needed base-devel devtools nvchecker pacman-contrib pyalpm shellcheck
```

Build all packages or one package:

```sh
./scripts/build
./scripts/build PACKAGE_NAME
```

Update versions, checksums, `.SRCINFO`, and the package table:

```sh
./scripts/update
./scripts/update PACKAGE_NAME
```

Use versioned build sources with pinned checksums. After a manual `PKGBUILD`
change, regenerate its metadata:

```sh
cd packages/PACKAGE_NAME
makepkg --printsrcinfo > .SRCINFO
cd ../..
```

Remove generated files:

```sh
./scripts/clean
```

## Automation

The daily updater opens or refreshes one PR. It uses a temporary `.netrc` for
GitHub checks and a GitHub App token to create signed commits and trigger PR
validation. Local checks can use `~/.netrc` (mode `0600`) for GitHub authentication.

Validation runs ShellCheck and builds all packages. **Merging does not publish
packages.** Publishing is manual.

## Publishing

Publishing requires the signing key and the `r2:pacshelf` rclone remote:

```sh
sudo pacman -S --needed gnupg rclone
git switch main
git pull --ff-only
./scripts/release all
```

To release one package from an existing repository:

```sh
./scripts/release PACKAGE_NAME
```

Use `pkgrel=1` for a new upstream version. Increment `pkgrel` when changing an
existing package without changing `pkgver`.

Release reuses verified published packages and builds only missing versions.
It uploads signed packages before the signed repository databases. Remote
package files are immutable; changed builds need a new package release.

## Packages

| Package | Version |
| --- | --- |
| [`aws-session-manager-plugin`](packages/aws-session-manager-plugin) | `1.2.896.0-1` |
| [`openai-chatgpt`](packages/openai-chatgpt) | `26.1111.21308-1` |
| [`proton-pass`](packages/proton-pass) | `1.43.1-1` |
| [`slack`](packages/slack) | `4.53.287-1` |
