This package requires the use of pyenv.
You need to compile it with python 2.7.18 active.
---
You need to install Yapps, globally using your system python pip
`pip install yapps`

When then switching environment it should take hold.
By doing `eval "$(pyenv init -)"` in the PKGBUILD folder you will
be able to switch into the locally selected python defined in .python_version.

This should build on both x86 and ARM as long s the packages can be obtained.

Once built and install the package with: `yay -U <packagename.zst>`
And then run it in place with "run-lcnc" that gets installed into /usr/bin

Package ends up in `/opt/linuxcnc` as a run in place installation.
