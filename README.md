# Creates a macOS installer for git

This is updated to create universal Intel + Apple Silicon binaries. It targets only macOS Sierra and later.
This optionally supports signing the executables and the installer.

**Note:** During the next release, support for Intel will be dropped. These were last present in 2020 models, up for
sale until 2023, will no longer be supported in macOS 27. If someone has one of those machines, they can use one of
the multitude of alternatives for getting an Intel version of git (e.g. `brew`).

## Install

Download the most recent release and run it. This installs to `/usr/local` on both Intel and Apple Silicon. This may conflict with brew's installed `git` on Intel.

## Uninstall

Run `/usr/local/bin/git/uninstall`

## Notes

This does not include:

* `gettext` support and thus does not include translations (macOS has never included a `gettext` library)
* `git-svn` (macOS does not include `svn`)

Optionally this does not include:

* `git gui` or `gitk` (macOS has deprecated the system Tk, doesn't seem to work at all anyways)
* Documentation for `git-subtree`

## To build a new package

Requires Xcode Command Line Tools (which can be installed with `xcode-select --install`) and optionally `brew` (see [brew.sh](https://brew.sh/), required for documentation). This also requires `rust`, not installed with `brew`:

```shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh  # install rust (cannot use brew's rust for cross-compiling?)
. "$HOME/.cargo/env"
```

If you wish to sign the executables and the installer package, include the `CERTIFICATE="..."` and `CERTIFICATE_INSTALLER="..."` options. The value must be the names of the certificates, for example, `"Developer ID Application: Author Name (Team ID)"` and `"Developer ID Installer: Author Name (Team ID)"`. This does not perform notarization, however. For that, see the `notarize` script.

Note: these build instructions are relatively obnoxious right now, I could improve them, but they will
simplify dramatically when Intel support is dropped.

```shell
# optional: brew install docbook docbook-xsl
rustup target add aarch64-apple-darwin x86_64-apple-darwin
git clone https://github.com/MoravianUniversity/git_osx_installer.git
cd git_osx_installer
OPTIONS=""  # options: INCLUDE_SUBTREE_DOC=1 INCLUDE_GUI=1 CERTIFICATE="..." CERTIFICATE_INSTALLER="..."
#make INCLUDE_SUBTREE_DOC=1 setup  # only required if including git-subtree documentation
make $OPTIONS package
# that will fail due to an issue with cross-compiling output directory (copy file)
mkdir -p build/git-arm/target/release
cp build/git-arm/target/aarch64-apple-darwin/release/libgitcore.a build/git-arm/target/release/libgitcore.a
make $OPTIONS package
# fails again, copy one more file
mkdir -p build/git-intel/target/release
cp build/git-intel/target/x86_64-apple-darwin/release/libgitcore.a build/git-intel/target/release/libgitcore.a
make $OPTIONS package # will finally succeed
#./notarize  # if creating a signed package, will need to provide Apple ID, Team ID, and App-Specific Password
```
