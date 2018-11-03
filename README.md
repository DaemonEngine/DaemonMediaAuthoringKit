Dæmon Media Authoring Kit
=========================

This is a CMake configuration to build software used to edit, build and package assets for Dæmon based games.

It installs [NetRadiant](https://gitlab.com/xonotic/netradiant#netradiant), [Chameleon](https://github.com/DaemonEngine/Chameleon#chameleon), [Urcheon](https://github.com/DaemonEngine/Urcheon#urcheon), [DaemonMap](https://github.com/DaemonEngine/daemonmap#daemonmap), Q3map2, IQM and [Crunch](https://github.com/DaemonEngine/crunch) in an `install` subdirectory.

You may need other dependencies, like GtkGlExt for NetRadiant, Python modules and tools like `convert`, `cwebp` and `opusenc` for Crunch ([read more about it](https://github.com/DaemonEngine/Urcheon#dependencies)), PyQt4 for Chameleon, and others.

# Build

```sh
cmake -H. -Bbuild
cmake --build build -- -j$(nproc)
```


# Usage

Add `install/bin` to your environment path, use the various tools at your confidence.


# License

CMake configuration is distributed under [CC0 1.0 license](LICENSE.txt).
