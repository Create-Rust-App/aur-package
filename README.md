# Create Rust App — AUR package mirror

GitHub mirror of the AUR packages:

| AUR package | Type | Install |
|-------------|------|---------|
| [`create-awesome-rust-app`](https://aur.archlinux.org/packages/create-awesome-rust-app) | Source build (`cargo`) | `yay -S create-awesome-rust-app` |
| [`create-awesome-rust-app-bin`](https://aur.archlinux.org/packages/create-awesome-rust-app-bin) | Prebuilt `linux-x86_64` | `yay -S create-awesome-rust-app-bin` |

Both provide `create-rust-app` / `create-awesome-rust-app` and conflict with each other.

> **Version policy:** the source package tracks the latest `create-rust-app@X.Y.Z`
> git tag; the `-bin` package tracks the latest *published* GitHub Release
> (draft releases have no stable download URL, so `-bin` lags tags until the
> release is published). Both `pkgver` values and checksums are verified
> against the upstream tag tarball and release `SHA256SUMS` on every bump.

## Install

With [yay](https://github.com/Jguer/yay):

```bash
yay -S create-awesome-rust-app      # source build (needs rust toolchain)
yay -S create-awesome-rust-app-bin  # prebuilt linux-x86_64 binary (faster)
```

With [paru](https://github.com/Morganamilo/paru):

```bash
paru -S create-awesome-rust-app
paru -S create-awesome-rust-app-bin
```

Pick **one**: the two packages conflict. Prefer `-bin` unless you want to build from source (requires `base-devel` and `rust`).

## Manual build

```bash
git clone https://github.com/Create-Rust-App/aur-package.git
cd aur-package/create-awesome-rust-app-bin
makepkg -si
```

## Version sync

`pkgver` in each `PKGBUILD` tracks the
[`create-rust-app@X.Y.Z`](https://github.com/Create-Rust-App/create-rust-app/releases)
GitHub Release tags. This mirror is updated by
[`publish-aur.yml`](https://github.com/Create-Rust-App/create-rust-app/blob/main/.github/workflows/publish-aur.yml)
in `create-rust-app` (GitHub environment `release`), so a new CLI release
usually lands here within minutes. If `yay -Syu` shows an older version than
the latest GitHub Release, the sync workflow is still running — check its
[runs](https://github.com/Create-Rust-App/create-rust-app/actions/workflows/publish-aur.yml).
`Validate PKGBUILD` warns on drift.

## Release sync checklist (maintainers)

When a new version lands as a GitHub Release:

1. Wait for the Release assets to be visible.
2. Bump `pkgver` in **both** `PKGBUILD`s (reset `pkgrel=1`).
3. Update `sha256sums` with the real checksums (source tarball + `linux-x86_64` asset).
4. Regenerate `.SRCINFO` in each directory (`makepkg --printsrcinfo > .SRCINFO`).
5. Push via PR — `Validate PKGBUILD` must stay green.

## Publishing to the AUR (maintainers)

Pushes to `main` that touch a `PKGBUILD` are published automatically by
[`publish-aur.yml`](.github/workflows/publish-aur.yml), using
[`ulises-jeremias/github-actions-aur-publish`](https://github.com/ulises-jeremias/github-actions-aur-publish)
(requires the `AUR_SSH_PRIVATE_KEY` secret). Manual fallback — each
subdirectory is pushed to its own AUR package, with SSH configured via
the `aur` host (`~/.ssh/config` → `aur.archlinux.org`):

```bash
cd create-awesome-rust-app
git remote add aur ssh://aur@aur.archlinux.org/create-awesome-rust-app.git
git push aur main:master
```

Repeat for `create-awesome-rust-app-bin`. Never push unreleased placeholder
checksums.

## Troubleshooting

- `target not found`: refresh your helper (`yay -Syu`) or install the AUR helper itself first; these packages live in the AUR, not the official repos.
- `conflicting packages`: you have both variants installed — remove one (`yay -Rns create-awesome-rust-app` or `...-bin`).
- Source build fails (`cargo` errors): install the toolchain (`sudo pacman -S --needed base-devel rust`) or switch to the `-bin` package.
- `makepkg` checksum errors: delete the cached sources (`~/.cache/yay/<pkg>`) and retry; persistent mismatches mean the mirror is mid-sync (see above).
- Still stuck: ask on [Discord](https://discord.gg/bR5VyATgka) or search the [CLI issues](https://github.com/Create-Rust-App/create-rust-app/issues).

Automation: [`publish-aur.yml`](https://github.com/Create-Rust-App/create-rust-app/blob/main/.github/workflows/publish-aur.yml) in `create-rust-app` (GitHub environment `release`).

## 👥 Contributors

<a href="https://github.com/Create-Rust-App/aur-package/contributors">
  <img src="https://contrib.rocks/image?repo=Create-Rust-App/aur-package"/>
</a>

Made with [contributors-img](https://contrib.rocks).
