Dæmon Media Authoring Kit
=========================

This is a CMake configuration to build software used to edit, build and package assets for Dæmon based games like [Unvanquished](https://unvanquished.net).

It installs [_NetRadiant_](https://gitlab.com/xonotic/netradiant#netradiant), [_Chameleon_](https://github.com/DaemonEngine/Chameleon#chameleon), [_Urcheon_](https://github.com/DaemonEngine/Urcheon#urcheon), [_DaemonMap_](https://github.com/DaemonEngine/daemonmap#daemonmap), _Q3map2_, _IQM_ and [_Crunch_](https://github.com/DaemonEngine/crunch) tools in an `install` subdirectory.

If not provided by the system, it will also build and install `cwebp`, `opusenc`, `oggenc` and `flac` tools.

You may need other dependencies, like _GtkGlExt_ for NetRadiant and other libraries ([read more about it](https://gitlab.com/xonotic/netradiant#dependencies)), _Python_ modules for Urcheon ([read more about it](https://github.com/DaemonEngine/Urcheon#dependencies)), _PyQt5_ for Chameleon, and others.

# Build

```sh
cmake -H. -Bbuild
cmake --build build -- -j$(nproc)
```


# Usage

Add `install/bin` to your environment path, use the various tools at your confidence.

```sh
export PATH="${PATH}:$(pwd)/install/bin'
```

```sh
netradiant
```


# Advanced build

By default the common `cwebp`, `opusenc`, `oggenc` and `flac` tools are not built if system provides them. You can force building everything (ignoring detection for existing tools) this way:

```sh
cmake -H. -Bbuild -DBUILD_ALL=ON
cmake --build build -- -j$(nproc)
```

You may need the `libtool` utility to properly build tools like `flac`, the `libtoolize` tool is not enough, `libtool` may be found in a package named like `libtool-bin`.


# License

This CMake configuration is distributed under [CC0 1.0 license](LICENSE.txt).
