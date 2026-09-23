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

Install the development tools on Arch Linux:

```sh
sudo pacman -S --needed base-devel devtools nvchecker pacman-contrib pyalpm shellcheck
```

Package definitions live under `packages/`. Build every package or one package:

```sh
./scripts/build
./scripts/build PACKAGE_NAME
```

Check every package or one package for an upstream update:

```sh
./scripts/update
./scripts/update PACKAGE_NAME
```

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

## Publishing

Publishing requires the repository signing key and the `r2:pacshelf` rclone
remote. Release one changed package, or initialize an empty repository from
all packages:

```sh
sudo pacman -S --needed gnupg rclone
git switch main
git pull --ff-only
./scripts/release PACKAGE_NAME
# or
./scripts/release all
```

Use `pkgrel=1` for a new upstream version. Increment `pkgrel` when changing an
existing package without changing `pkgver`.

## Packages

- `aws-session-manager-plugin`
- `openai-chatgpt`
- `proton-pass`
- `slack`
