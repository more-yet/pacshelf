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

Repository layout:

- `packages/`: each directory owns its `PKGBUILD`, generated `.SRCINFO`,
  `.nvchecker.toml` update configuration, and supporting files.
- `scripts/`: shared build, update, cleanup, and release commands.
- `.github/workflows/`: scheduled update PRs and build validation.

Install the development tools on Arch Linux:

```sh
sudo pacman -S --needed base-devel devtools nvchecker pacman-contrib pyalpm shellcheck
```

Build every package or one package. The build script checks `.SRCINFO` against
the `PKGBUILD` before running `makepkg`:

```sh
./scripts/build
./scripts/build PACKAGE_NAME
```

Check every package or one package for an upstream update using `pkgctl` and
nvchecker. Updates refresh versions, checksums, `.SRCINFO`, and the package table
at the end of this README:

```sh
./scripts/update
./scripts/update PACKAGE_NAME
```

Build sources should use versioned URLs and pinned checksums. Mutable release
indexes belong in update discovery, not build inputs.

After editing a `PKGBUILD` manually, regenerate its metadata:

```sh
cd packages/PACKAGE_NAME
makepkg --printsrcinfo > .SRCINFO
cd ../..
```

Remove generated packages, downloaded sources, build directories, and local
repository files:

```sh
./scripts/clean
```

## Automation

The scheduled updater opens or refreshes one package-update PR. It uses the
read-only workflow token for upstream GitHub checks through a temporary,
mode-`0600` `.netrc`, removed when the update step exits. A separate GitHub App
token creates the PR so validation runs. Local checks can use a GitHub token in
`~/.netrc` (mode `0600`) if unauthenticated requests are rate-limited.

Validation runs ShellCheck and builds every package. ShellCheck excludes
`SC1091` because runtime configuration files are generated during packaging.
Publishing is a separate, manual operation.

## Publishing

Publishing requires the repository signing key and the `r2:pacshelf` rclone
remote. Release one changed package, or initialize an empty repository from
all packages:

```sh
sudo pacman -S --needed gnupg rclone
git switch main
git pull --ff-only
./scripts/release PACKAGE_NAME
```

Alternatively, initialize the repository with every package:

```sh
./scripts/release all
```

Use `pkgrel=1` for a new upstream version. Increment `pkgrel` when changing an
existing package without changing `pkgver`.

## Packages

| Package | Version |
| --- | --- |
| [`aws-session-manager-plugin`](packages/aws-session-manager-plugin) | `1.2.896.0-1` |
| [`openai-chatgpt`](packages/openai-chatgpt) | `26.1007.21434-2` |
| [`proton-pass`](packages/proton-pass) | `1.42.0-1` |
| [`slack`](packages/slack) | `4.52.178-2` |
