# PacShelf

Custom Arch Linux packages, published at
[`pacshelf.moreyet.com`](https://pacshelf.moreyet.com).

## Setup

Run this once to trust the PacShelf signing key and add the repository to
Pacman:

```bash
( set -euo pipefail
  sudo pacman-key --init
  curl -fsSL --proto '=https' https://pacshelf.moreyet.com/pacshelf.asc |
    sudo pacman-key --add
  sudo pacman-key --lsign-key 9243E163FD44CCF22FF9C29E6F582689B321A703
  sudo tee -a /etc/pacman.conf >/dev/null <<'EOF'

[pacshelf]
SigLevel = PackageRequired DatabaseRequired TrustedOnly
Server = https://pacshelf.moreyet.com/$arch
EOF
)
```

The full signing-key fingerprint is
`9243 E163 FD44 CCF2 2FF9 C29E 6F58 2689 B321 A703`.

List or install packages with a full system upgrade:

```bash
pacman -Sl pacshelf
sudo pacman -Syu <package>
```

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
changing its upstream version. The release process retains the latest three
versions of each package locally and remotely for bounded rollback history.

GitHub Actions validates package changes and checks for upstream releases. The
update workflow uses a repository-scoped GitHub App to open pull requests with
signed commits.

For each update pull request, review the package changes, wait for validation
to pass, and squash-merge it. Then publish from the release host:

```bash
git switch main
git pull --ff-only
./scripts/release <package>
```

## Packages

- `aws-session-manager-plugin`
- `openai-chatgpt`
- `proton-pass`
- `slack`
