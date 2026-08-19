# Creates a macOS installer for git

Creates Apple Silicon binaries for git along with an installer. It targets only macOS Sierra and later.
This optionally supports signing the executables and the installer.

**Note:** Support for Intel has been dropped. Last present in 2020 models, up for sale until 2023, no longer
supported in macOS 27. If someone has one of those machines, they can use one of the alternatives for getting
an Intel version of git (e.g. `brew`).

## Install

Download the most recent release and run it. This installs to `/usr/local`.

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

Requires Xcode Command Line Tools (which can be installed with `xcode-select --install`) and `brew` (see [brew.sh](https://brew.sh/), required for `rust` and documentation).

If you wish to sign the executables and the installer package, include the `CERTIFICATE="..."` and `CERTIFICATE_INSTALLER="..."` options. The value must be the names of the certificates, for example, `"Developer ID Application: Author Name (Team ID)"` and `"Developer ID Installer: Author Name (Team ID)"`. This does not perform notarization, however. For that, see the `notarize` script.

```shell
brew install rust # optional: docbook docbook-xsl
git clone https://github.com/MoravianUniversity/git_osx_installer.git
cd git_osx_installer
#make INCLUDE_SUBTREE_DOC=1 setup  # only required if including git-subtree documentation
make package # options: INCLUDE_SUBTREE_DOC=1 INCLUDE_GUI=1 CERTIFICATE="..." CERTIFICATE_INSTALLER="..."
#./notarize  # if creating a signed package, will need to provide Apple ID, Team ID, and App-Specific Password
```
