# collabora-core-assets

Pre-built Collabora Office core assets for the
[collabora-office](https://aur.archlinux.org/packages/collabora-office) AUR
package.

## What this builds

A GitHub Actions workflow clones the Collabora Office monorepo from Gerrit at a
tagged release, builds the `engine/` subdirectory with the `CPLinux-LOKit`
distro config, and uploads the result as a GitHub release asset.

The tarball contains:
- `instdir/` - Collabora Office engine runtime (program/, share/, presets/, sdk/)
- `include/` - Core headers (LibreOfficeKit/)

## Triggering a build

Go to **Actions > Build Core Assets > Run workflow**. You can specify a source
tag (e.g. `cp-26.04.2-1`) or leave it empty to auto-detect the latest
`cp-26.04*` tag.

Scheduled builds run weekly and skip if a release for the latest tag already
exists.

## Using in the PKGBUILD

Update the `source=()` URL to point to a release from this repo:

```bash
source=(
    "online::git+https://gerrit.collaboraoffice.com/online#tag=<tag>"
    "https://github.com/Rash419/collabora-core-assets/releases/download/<tag>/core-co-26.04-assets.tar.gz"
)
```

Update the `sha256sums` to match (check the `.sha256` file in the release).

## Build configuration

The workflow uses `--with-distro=CPLinux-LOKit` which loads flags from
`engine/distro-configs/CPLinux-LOKit.conf` in the monorepo source. Key settings:

- `--enable-mergelibs=more` - single merged library for performance
- `--enable-release-build`
- `--disable-gui` / `--disable-gtk3` / `--disable-qt5` - headless LOKit build
- `--without-java`
- `--enable-python=internal`
- Most system libraries built internally (`--without-system-*`)

Overrides applied by the workflow:
- `--without-package-format` - only instdir is needed, not deb/rpm
- `--with-system-nss` - matches the documented 26.04 Linux desktop build
- `--disable-symbols` - keeps the tarball small
- `--disable-sal-log` - not needed for release builds
