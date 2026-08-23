# PacShelf

Custom Arch Linux packages, published at
[`pacshelf.moreyet.com`](https://pacshelf.moreyet.com).

## Setup

Download the repository key and verify its fingerprint:

```bash
curl -fsSLo /tmp/pacshelf.asc https://pacshelf.moreyet.com/pacshelf.asc
gpg --show-keys --fingerprint /tmp/pacshelf.asc
sudo pacman-key --add /tmp/pacshelf.asc
sudo pacman-key --lsign-key 9243E163FD44CCF22FF9C29E6F582689B321A703
```

Add the repository to `/etc/pacman.conf`:

```ini
[pacshelf]
SigLevel = PackageRequired DatabaseOptional TrustedOnly
Server = https://pacshelf.moreyet.com/$arch
```

Then update Pacman:

```bash
sudo pacman -Syu
pacman -Sl pacshelf
sudo pacman -S <package>
```

The `slack` package contains an open-source launcher. On first run it downloads
the pinned Slack release directly from Slack, verifies it, and installs it in
the current user's data directory; PacShelf does not distribute Slack binaries.

## Development

Package definitions live under `packages/`. Build and validate one with:

```bash
./scripts/pipeline <package>
```

Check for an upstream release with:

```bash
./scripts/update <package>
```

`update` may modify the `PKGBUILD` and `.SRCINFO`; review both before
committing.

## Publishing

Publishing runs from the release host, which keeps the signing key, rclone
credentials, and the persistent `repo/` directory.

```bash
./scripts/release all
```

To publish only one package:

```bash
./scripts/release <package>
```

Package updates are immutable, so bump `pkgrel` when changing a package without
changing its upstream version.

GitHub Actions validates package changes and checks for upstream releases. The
update workflow uses a repository-scoped GitHub App to open signed pull
requests.

For each verified update pull request, review the package changes, wait for
validation to pass, and squash-merge it. Then publish from the release host:

```bash
git switch main
git pull --ff-only
./scripts/release <package>
```
