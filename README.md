# collabora-core-assets

Pre-built LibreOffice core assets for the
[collabora-office](https://aur.archlinux.org/packages/collabora-office) AUR
package.

## What this builds

A GitHub Actions workflow clones LibreOffice core from the
`distro/collabora/co-25.04` branch at a tagged release, builds it with the
`CPLinux-LOKit` distro config, and uploads the result as a GitHub release
asset.

The tarball contains:
- `instdir/` - LibreOffice core runtime (program/, share/, presets/, sdk/)
- `include/` - Core headers (LibreOfficeKit/)

## Triggering a build

Go to **Actions > Build Core Assets > Run workflow**. You can specify a core
tag (e.g. `co-25.04.10-1`) or leave it empty to auto-detect the latest
`co-25.04*` tag.

Scheduled builds run weekly and skip if a release for the latest tag already
exists.

## Using in the PKGBUILD

Update the `source=()` URL to point to a release from this repo:

```bash
source=(
    "git+https://github.com/CollaboraOnline/online.git#branch=distro/collabora/coda-25.04"
    "https://github.com/Rash419/collabora-core-assets/releases/download/<tag>/core-co-25.04-assets.tar.gz"
)
```

Update the `sha256sums` to match (check the `.sha256` file in the release).

## Build configuration

The workflow uses `--with-distro=CPLinux-LOKit` which loads flags from
`distro-configs/CPLinux-LOKit.conf` in the core source. Key settings:

- `--enable-mergelibs=more` - single merged library for performance
- `--enable-release-build`
- `--disable-gui` / `--disable-gtk3` / `--disable-qt5` - headless LOKit build
- `--without-java`
- `--enable-python=internal`
- Most system libraries built internally (`--without-system-*`)

Overrides applied by the workflow:
- `--without-package-format` - only instdir is needed, not deb/rpm
- `--disable-symbols` - keeps the tarball small
- `--disable-sal-log` - not needed for release builds
