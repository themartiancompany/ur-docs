[comment]: <> (SPDX-License-Identifier: AGPL-3.0)

[comment]: <> (-------------------------------------------------------------)
[comment]: <> (Copyright © 2024, 2025, 2026  Pellegrino Prevete)
[comment]: <> (All rights reserved)
[comment]: <> (-------------------------------------------------------------)

[comment]: <> (This program is free software: you can redistribute)
[comment]: <> (it and/or modify it under the terms of the GNU Affero)
[comment]: <> (General Public License as published by the Free)
[comment]: <> (Software Foundation, either version 3 of the License.)

[comment]: <> (This program is distributed in the hope that it will be useful,)
[comment]: <> (but WITHOUT ANY WARRANTY; without even the implied warranty of)
[comment]: <> (MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the)
[comment]: <> (GNU Affero General Public License for more details.)

[comment]: <> (You should have received a copy of the GNU Affero General Public)
[comment]: <> (License along with this program.)
[comment]: <> (If not, see <https://www.gnu.org/licenses/>.)

# Universal Recipes

Ur
[PKGBUILD](
  https://wiki.archlinux.org/title/PKGBUILD)s
are also called *universal recipes*, because they
are supposed to run on all Life and DogeOS supported
base platforms, such as GNU/Linux, Android and Windows.

In particular, this makes Ur recipes seamlessly compatible
with
[Arch Linux](
  https://archlinux.org),
with the Pacman-based
[Termux](
  https://termux.dev)
Android runtime environment and with the
[MSYS2](
  https://msys2.org)
Windows runtime environments, given they are built using
[`reallymakepkg`](
  https://github.com/themartiancompany/reallymakepkg).
Support for macOS and iOS is currently to be set up
manually and it has been tested in the
[`ish`](
  https://github.com/ish-app/ish)
and
[`brew`](
  https://github.com/homebrew/brew)
environments.

While Ur recipes and packages are compatible with the
all of the above platforms, at least Arch Linux developers
explicitly forbid publishing on their repos recipes which are
compatible with many operating systems or platforms at once.

For Termux it has been instead under evaluation
[merging the Tur into the Ur](
  https://github.com/termux-user-repository/tur/issues/1486)
but it's now on hold.

Ur authors see hard the above projects will
hardly support the application store directly,
unless they are gonna make it their default,
as its unique properties (i.e.
[undeletability](
  https://github.com/themartiancompany/evmfs-docs/blob/master/README.md),
permissionlessness, cross-platform support)
makes from their point of view inevitable
that the Ur will eventually become the main
publishing mechanism for computer applications
on the internet, as it is orders of magnitude
more reliable and cheaper than any other existing
way of publishing content on the internet.

## Differences with `PKGBUILD`s

While one can indeed publish standard normal `PKGBUILD`s
as Universal Recipes on the Ur, it is highly suggested
the following general rules are respected.

### DogeOS coding style

Universal recipes are expected to respect
[DogeOS coding style](
  https://github.com/themartiancompany/dogeos-coding-style)
for greater code readability on smartphones
and portrait views.

### Mandatory fields

For better syntax highlighting the following convention is
generally suggested for adoption when writing
the now mandatory `pkgbase`; the `pkgname` variable is also
generally intended as an array and treated as such.

```bash
_pkg=package-name
pkgbase="${_pkg}"
pkgname=(
  "${_pkg}"
)
```

### Switches

Generally speaking, cross-platform changes
which are currently not handled automatically by
[Reallymakepkg](
  https://github.com/themartiancompany),
the recipes do make large use of build switches
set through bash variables of the form `_variable_name`,
usually of boolean (`true`, `false`) or general
string types.

Switches must be generally be overridable and
their value is usually auto-detected from the running
system.

Common switches are:

- `_evmfs` (`bool`):
    Retrieve resources from the EVMFS.

- `_git` (`bool`):
    Whether to use Git to retrieve Git repositories;
    the presence of this switch depends on the fact
    platforms like Github and Gitlab do offer tarballs
    (archive files).

- `_docs` (`bool`):
     Whether to build the documentation packages.

### Common variables

Some switches enable the evaluation of switch-specific
variables:

- `_git_service` (`string`):
    Common value for this variable are `gitlab` and
    `github`.

- `_tag_name` (`string`):
    Usually either "pkgver" or "commit"

- `_tag` (`string`):
    By default the value of the `pkgver` or `_commit`
    variables

- `_ns` (`string`):
     Namespace name (for Git, EVMFS).

### Cross-platform variables

Being Universal Recipes cross-platform,
variables are used to tell `makepkg`
which compilers and library to build
against on a given base operating system.
This is done usually by using the
variables described in the following.
In time some or all of these variables will
be eventually auto-detected by
`reallymakepkg`.
For specific documentation about which runtime
variables to set and which `depends` and `makedepends`
are to be set in an Universal recipe to enable
specific OSes C programs compiling, consult `reallymakepkg`
[documentation](
  https://github.com/themartiancompany/reallymakepkg).

- `_libc` (`string`):
    On GNU/Linux it's usually `glibc`, on Android
    it's `ndk-sysroot`, on Windows `glibc`.

- `_compiler` (`string`):
    The C compiler. On GNU/Linux usually `gcc`,
    on Android `clang` and on Windows `gcc`.

- `_libcompiler` (`string`):
    The C compiler library. On GNU/Linux usually
    `gcc-libs` (more recently also `libgcc`), on
    Android `llvm-libs` and on Windows same as GNU.

- `_libc_headers` (`string`):
    This is Windows specific at the moment and it
    must have value `msys2-w32api-headers`.

## Packaging guidelines

Ur publishers should make sure to respect the following
packaging guidelines in order to streamline
applications public and private security and quality
assurance reviewing:

- all package resources must be stored on the
  [EVMFS](
    https://github.com/themartiancompany);
- packages should be
  [Gur](
    https://github.com/themartiancompany/gur)-compatible;
  in practice this means:
  - for a package called `package` its repository name on
    Github/Gitlab should be `package-ur`;
  - recipes directories must contain continuous
    integration configuration and build files for at least one
    online continuous integration system (for example
    Gitlab or Github); displayed platform compatibility
    on the store, except when set manually, is automatically
    determined on whether the program package includes those
    files and builds on one of those services;

### Language-specific rules

#### Javascript modules

Given a Node.js module library called `example-library`,
the correspondent package should have name
`nodejs-example-library`.

Given a Node.js module `example-program` contains a program
with the same name, then the correspondent program
should be called `example-program`.

Whenever possible or whenever not managed through,
the `example-program` module must be symlinked or
installed to `/usr/lib/example-program` or
`/usr/lib/example-program/nodejs`;
the reason for the above is to allow the
[Crash Javascript](
  https://github.com/themartiancompany/crash-js)'s
`_require` function to seamlessly load modules either
from Node.js modules' directory or from system libraries'
directory directly.

Given a Node.js module which contain programs should be symlinked in
`/usr/lib`
Given a Node.js module contains

## Examples

For an updated reference Universal Recipe `PKGBUILD` you
can consult the one for the
[`evmfs`](
  https://github.com/themartiancompany/evmfs-ur).

As you notice most of the extra code compared
to a regular `PKGBUILD` is exclusively relative
to mantaining the recipe compatible with systems who do not
have available the `evmfs` command, so
one of the vanilla base operating systems without any
HIP component installed.

Whenever this retro-compatibility requirement
can be omitted, for example for all the software
which is not distributed through HTTP mirrors,
you see the recipes are regular Arch Linux `PKGBUILD`s.

### Compatibility with non-Pacman based runtime environment

While foreseen in a future release, recipe compatibility
with packagement systems such as `dpkg` and `rpm` is
currently not implemented.

Contributions are welcome.
