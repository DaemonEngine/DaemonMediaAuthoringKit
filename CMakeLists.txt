# Author: Thomas Debesse
# License: CC0 1.0

cmake_minimum_required(VERSION 3.1)

include(${CMAKE_ROOT}/Modules/ExternalProject.cmake)

project(DaemonADK)

if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set(CMAKE_INSTALL_PREFIX "${PROJECT_SOURCE_DIR}/install" CACHE PATH "..." FORCE)
endif ()

ExternalProject_Add(netradiant
    PREFIX ExternalProjects/netradiant
    GIT_REPOSITORY https://gitlab.com/xonotic/netradiant.git
    CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
    CMAKE_ARGS -DFHS_INSTALL=ON
    CMAKE_ARGS -DDOWNLOAD_GAMEPACKS=ON
    CMAKE_ARGS -DGAMEPACKS_LICENSE_LIST=none
    CMAKE_ARGS -DGAMEPACKS_NAME_LIST=Unvanquished\ SmokinGuns
    CMAKE_ARGS -DBUILD_RADIANT=ON
    CMAKE_ARGS -DBUILD_TOOLS=ON
    CMAKE_ARGS -DBUILD_DAEMONMAP=ON
    CMAKE_ARGS -DBUILD_CRUNCH=ON
)

# NetRadiant already takes care of it
# ExternalProject_Add(daemonmap
#     PREFIX ExternalProjects/daemonmap
#     GIT_REPOSITORY https://github.com/DaemonEngine/daemonmap.git
#     CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
#     CMAKE_ARGS -DFHS_INSTALL=ON
# )

ExternalProject_Add(crunch
    PREFIX ExternalProjects/crunch
    GIT_REPOSITORY https://github.com/DaemonEngine/crunch.git
    CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
)

ExternalProject_Add(sloth
    PREFIX ExternalProjects/sloth
    GIT_REPOSITORY https://github.com/DaemonEngine/Sloth.git
    CONFIGURE_COMMAND echo
    BUILD_COMMAND echo
    INSTALL_COMMAND cp -a ../sloth/sloth.py ${CMAKE_INSTALL_PREFIX}/bin/sloth
)

ExternalProject_Add(chameleon
    PREFIX ExternalProjects/chameleon
    GIT_REPOSITORY https://github.com/DaemonEngine/Chameleon.git
    CONFIGURE_COMMAND echo
    BUILD_COMMAND echo
    INSTALL_COMMAND mkdir -p ${CMAKE_INSTALL_PREFIX}/bin
    INSTALL_COMMAND && cp -a ../chameleon/chameleon.py ${CMAKE_INSTALL_PREFIX}/bin/chameleon
)

ExternalProject_Add(urcheon
    PREFIX ExternalProjects/urcheon
    GIT_REPOSITORY https://github.com/DaemonEngine/Urcheon.git
    CONFIGURE_COMMAND echo
    BUILD_COMMAND echo
    INSTALL_COMMAND mkdir -p ${CMAKE_INSTALL_PREFIX}/bin
    INSTALL_COMMAND mkdir -p ${CMAKE_INSTALL_PREFIX}/Urcheon
    INSTALL_COMMAND mkdir -p ${CMAKE_INSTALL_PREFIX}/profile
    INSTALL_COMMAND && cp -a ../urcheon/Urcheon/. ${CMAKE_INSTALL_PREFIX}/Urcheon/.
    INSTALL_COMMAND && cp -a ../urcheon/profile/. ${CMAKE_INSTALL_PREFIX}/profile/.
    INSTALL_COMMAND && cp -a ../urcheon/bin/urcheon ${CMAKE_INSTALL_PREFIX}/bin/urcheon
    INSTALL_COMMAND && cp -a ../urcheon/bin/esquirel ${CMAKE_INSTALL_PREFIX}/bin/esquirel
)

ExternalProject_Add(iqm
    PREFIX ExternalProjects/iqm
    SVN_REPOSITORY https://svn.code.sf.net/p/fteqw/code/trunk/iqm
    SVN_REVISION -r5570
    CONFIGURE_COMMAND echo
    BUILD_COMMAND $(MAKE) -C ../iqm
    BUILD_COMMAND && strip ../iqm/iqm
    INSTALL_COMMAND mkdir -p ${CMAKE_INSTALL_PREFIX}/bin
    INSTALL_COMMAND && cp ../iqm/iqm ${CMAKE_INSTALL_PREFIX}/bin/iqm
)

ExternalProject_Add(libwebp
    PREFIX ExternalProjects/libwebp
    GIT_REPOSITORY https://chromium.googlesource.com/webm/libwebp
    CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
    CMAKE_ARGS -DWEBP_BUILD_ANIM_UTILS=OFF
    CMAKE_ARGS -DWEBP_BUILD_CWEBP=ON
    CMAKE_ARGS -DWEBP_BUILD_DWEBP=ON
    CMAKE_ARGS -DWEBP_BUILD_EXTRAS=OFF
    CMAKE_ARGS -DWEBP_BUILD_GIF2WEBP=OFF
    CMAKE_ARGS -DWEBP_BUILD_IMG2WEBP=OFF
    CMAKE_ARGS -DWEBP_BUILD_VWEBP=OFF
    CMAKE_ARGS -DWEBP_BUILD_WEBPINFO=ON
    CMAKE_ARGS -DWEBP_BUILD_WEBPMUX=OFF
    CMAKE_ARGS -DWEBP_BUILD_WEBP_JS=OFF
    CMAKE_ARGS -DWEBP_NEAR_LOSSLESS=ON
)

ExternalProject_Add(flac
    PREFIX ExternalProjects/flac
    GIT_REPOSITORY https://gitlab.xiph.org/xiph/flac.git
    BUILD_IN_SOURCE ON
    CONFIGURE_COMMAND ./autogen.sh
    CONFIGURE_COMMAND && ./configure --prefix=${CMAKE_INSTALL_PREFIX}
    BUILD_COMMAND $(MAKE)
    INSTALL_COMMAND $(MAKE) install
)

ExternalProject_Add(ogg
    PREFIX ExternalProjects/ogg
    GIT_REPOSITORY https://gitlab.xiph.org/xiph/ogg.git
    BUILD_IN_SOURCE ON
    CONFIGURE_COMMAND ./autogen.sh
    CONFIGURE_COMMAND && ./configure --prefix=${CMAKE_INSTALL_PREFIX}
    BUILD_COMMAND $(MAKE)
    INSTALL_COMMAND $(MAKE) install
)

ExternalProject_Add(vorbis
    PREFIX ExternalProjects/vorbis
    GIT_REPOSITORY https://gitlab.xiph.org/xiph/vorbis.git
    BUILD_IN_SOURCE ON
    CONFIGURE_COMMAND ./autogen.sh
    CONFIGURE_COMMAND && ./configure --prefix=${CMAKE_INSTALL_PREFIX}
    BUILD_COMMAND $(MAKE)
    INSTALL_COMMAND $(MAKE) install
)

ExternalProject_Add(vorbis-tools
    DEPENDS vorbis ogg
    PREFIX ExternalProjects/vorbis-tools
    GIT_REPOSITORY https://gitlab.xiph.org/xiph/vorbis-tools.git
    BUILD_IN_SOURCE ON
    CONFIGURE_COMMAND ./autogen.sh
    CONFIGURE_COMMAND && PKG_CONFIG_PATH=${CMAKE_INSTALL_PREFIX}/lib/pkgconfig
    CONFIGURE_COMMAND ./configure --prefix=${CMAKE_INSTALL_PREFIX}
    CONFIGURE_COMMAND --disable-vcut --disable-ogg123
    BUILD_COMMAND $(MAKE)
    INSTALL_COMMAND $(MAKE) install
)

ExternalProject_Add(opus
    PREFIX ExternalProjects/opus
    GIT_REPOSITORY https://gitlab.xiph.org/xiph/opus.git
    BUILD_IN_SOURCE ON
    CONFIGURE_COMMAND ./autogen.sh
    CONFIGURE_COMMAND && ./configure --prefix=${CMAKE_INSTALL_PREFIX}
    BUILD_COMMAND $(MAKE)
    INSTALL_COMMAND $(MAKE) install
)

ExternalProject_Add(opusfile
    DEPENDS opus ogg
    PREFIX ExternalProjects/opusfile
    GIT_REPOSITORY https://gitlab.xiph.org/xiph/opusfile.git
    BUILD_IN_SOURCE ON
    CONFIGURE_COMMAND ./autogen.sh
    CONFIGURE_COMMAND && ./configure --prefix=${CMAKE_INSTALL_PREFIX}
    BUILD_COMMAND $(MAKE)
    INSTALL_COMMAND $(MAKE) install
)

ExternalProject_Add(libopusenc
    DEPENDS opus
    PREFIX ExternalProjects/libopusenc
    GIT_REPOSITORY https://gitlab.xiph.org/xiph/libopusenc.git
    BUILD_IN_SOURCE ON
    CONFIGURE_COMMAND ./autogen.sh
    CONFIGURE_COMMAND && ./configure --prefix=${CMAKE_INSTALL_PREFIX}
    BUILD_COMMAND $(MAKE)
    INSTALL_COMMAND $(MAKE) install
)

ExternalProject_Add(opus-tools
    DEPENDS libopusenc opusfile opus ogg
    PREFIX ExternalProjects/opus-tools
    GIT_REPOSITORY https://gitlab.xiph.org/xiph/opus-tools.git
    BUILD_IN_SOURCE ON
    CONFIGURE_COMMAND ./autogen.sh
    CONFIGURE_COMMAND && PKG_CONFIG_PATH=${CMAKE_INSTALL_PREFIX}/lib/pkgconfig
    CONFIGURE_COMMAND ./configure --prefix=${CMAKE_INSTALL_PREFIX}
    BUILD_COMMAND $(MAKE)
    INSTALL_COMMAND $(MAKE) install
)
