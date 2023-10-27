# Author: Thomas Debesse
# License: CC0 1.0

cmake_minimum_required(VERSION 3.1)

include(${CMAKE_ROOT}/Modules/ExternalProject.cmake)

project(DaemonMediaAuthoringKit)

if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
	set(CMAKE_INSTALL_PREFIX "${PROJECT_SOURCE_DIR}/install" CACHE PATH "Install path prefix, prepended onto install directories." FORCE)
endif ()

find_program(SYSTEM_CWEBP cwebp NO_CACHE)
find_program(SYSTEM_OGGENC oggenc NO_CACHE)
find_program(SYSTEM_OPUSENC opusenc NO_CACHE)
find_program(SYSTEM_FLAC flac NO_CACHE)

mark_as_advanced(SYSTEM_CWEBP)
mark_as_advanced(SYSTEM_OGGENC)
mark_as_advanced(SYSTEM_OPUSENC)
mark_as_advanced(SYSTEM_FLAC)

if (BUILD_ALL OR NOT SYSTEM_CWEBP)
	set(DEFAULT_BUILD_CWEBP ON)
else ()
	set(DEFAULT_BUILD_CWEBP OFF)
endif ()

if (BUILD_ALL OR NOT SYSTEM_OPUSENC)
	set(DEFAULT_BUILD_OPUSENC ON)
else ()
	set(DEFAULT_BUILD_OPUSENC OFF)
endif ()

if (BUILD_ALL OR NOT SYSTEM_OGGENC)
	set(DEFAULT_BUILD_OGGENC ON)
else ()
	set(DEFAULT_BUILD_OGGENC OFF)
endif ()

if (BUILD_ALL OR NOT SYSTEM_FLAC)
	set(DEFAULT_BUILD_FLAC ON)
else ()
	set(DEFAULT_BUILD_FLAC OFF)
endif ()

option(BUILD_NETRADIANT "Build NetRadiant level editor" ON)
option(BUILD_CHAMELEON "Build Chameleon texture replacement editor" ON)
option(BUILD_URCHEON "Build Urcheon build tool" ON)
option(BUILD_SLOTH "Build Sloth material generator" ON)
option(BUILD_Q3MAP2 "Build q3map2 map compiler and other tools" ON)
option(BUILD_IQMTOOL "Build iqmtool model converter" ON)
option(BUILD_CRUNCH "Build crunch image converter" ON)
option(BUILD_CWEBP "Build cwebp image converter and other tools" ${DEFAULT_BUILD_CWEBP})
option(BUILD_OPUSENC "Build opusenc sound converter and other tools" ${DEFAULT_BUILD_OPUSENC})
option(BUILD_OGGENC "Build oggenc vorbis sound converter and other tools" ${DEFAULT_BUILD_OGGENC})
option(BUILD_FLAC "Build flac sound converter" ${DEFAULT_BUILD_FLAC})

set(GIT_REPOSITORY_NETRADIANT "https://gitlab.com/xonotic/netradiant.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_CRUNCH "https://github.com/DaemonEngine/crunch.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_SLOTH "https://github.com/DaemonEngine/Sloth.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_CHAMELEON "https://github.com/DaemonEngine/Chameleon.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_URCHEON "https://github.com/DaemonEngine/Urcheon.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_IQMTOOL "https://github.com/fte-team/fteqw.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_CWEBP "https://chromium.googlesource.com/webm/libwebp.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_OGG "https://gitlab.xiph.org/xiph/ogg.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_FLAC "https://gitlab.xiph.org/xiph/flac.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_VORBIS "https://gitlab.xiph.org/xiph/vorbis.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_VORBISTOOLS "https://gitlab.xiph.org/xiph/vorbis-tools.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_OPUS "https://gitlab.xiph.org/xiph/opus.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_OPUSFILE "https://gitlab.xiph.org/xiph/opusfile.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_OPUSENC "https://gitlab.xiph.org/xiph/libopusenc.git" CACHE STRING "Git repository location")
set(GIT_REPOSITORY_OPUSTOOLS "https://gitlab.xiph.org/xiph/opus-tools.git" CACHE STRING "Git repository location")

mark_as_advanced(GIT_REPOSITORY_NETRADIANT)
mark_as_advanced(GIT_REPOSITORY_CRUNCH)
mark_as_advanced(GIT_REPOSITORY_SLOTH)
mark_as_advanced(GIT_REPOSITORY_CHAMELEON)
mark_as_advanced(GIT_REPOSITORY_URCHEON)
mark_as_advanced(GIT_REPOSITORY_IQMTOOL)
mark_as_advanced(GIT_REPOSITORY_CWEBP)
mark_as_advanced(GIT_REPOSITORY_OGG)
mark_as_advanced(GIT_REPOSITORY_FLAC)
mark_as_advanced(GIT_REPOSITORY_VORBIS)
mark_as_advanced(GIT_REPOSITORY_VORBISTOOLS)
mark_as_advanced(GIT_REPOSITORY_OPUS)
mark_as_advanced(GIT_REPOSITORY_OPUSFILE)
mark_as_advanced(GIT_REPOSITORY_OPUSENC)
mark_as_advanced(GIT_REPOSITORY_OPUSTOOLS)

if (NOT CMAKE_BUILD_TYPE)
	set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel ..." FORCE)
endif()

if (BUILD_NETRADIANT)
	set(NETRADIANT_GAMEPACKS_DOC "NetRadiant gamepacks to install")
	set(NETRADIANT_GAMEPACKS "Unvanquished" CACHE STRING ${NETRADIANT_GAMEPACKS_DOC})

	if (NOT NETRADIANT_GAMEPACKS)
		set(NETRADIANT_GAMEPACKS "none" CACHE STRING ${NETRADIANT_GAMEPACKS_DOC} FORCE)
	endif()
endif()

if (BUILD_NETRADIANT OR BUILD_Q3MAP2)
	ExternalProject_Add(netradiant
		PREFIX ExternalProjects/netradiant
		GIT_REPOSITORY ${GIT_REPOSITORY_NETRADIANT}
		CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
		CMAKE_ARGS -DFHS_INSTALL=ON

		# Always build NetRadiant and q3map2 with CRN image support
		# even if crunch tool is not build.
		CMAKE_ARGS -DBUILD_CRUNCH=ON

		if (BUILD_NETRADIANT)
			CMAKE_ARGS -DDOWNLOAD_GAMEPACKS=ON
			CMAKE_ARGS -DGAMEPACKS_LICENSE_LIST=none
			CMAKE_ARGS -DGAMEPACKS_NAME_LIST=${NETRADIANT_GAMEPACKS}
			CMAKE_ARGS -DBUILD_RADIANT=ON
		endif ()

		if (BUILD_Q3MAP2)
			CMAKE_ARGS -DBUILD_TOOLS=ON
		endif ()
	)
endif()

if (BUILD_CRUNCH)
	ExternalProject_Add(crunch
		PREFIX ExternalProjects/crunch
		GIT_REPOSITORY ${GIT_REPOSITORY_CRUNCH}
		CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
	)
endif ()

if (BUILD_SLOTH)
	ExternalProject_Add(sloth
		PREFIX ExternalProjects/sloth
		GIT_REPOSITORY ${GIT_REPOSITORY_SLOTH}
		CONFIGURE_COMMAND echo
		BUILD_COMMAND echo
		INSTALL_COMMAND mkdir -p ${CMAKE_INSTALL_PREFIX}/bin
		INSTALL_COMMAND && cp -a ../sloth/sloth.py ${CMAKE_INSTALL_PREFIX}/bin/sloth
	)
endif ()

if (BUILD_CHAMELEON)
	ExternalProject_Add(chameleon
		PREFIX ExternalProjects/chameleon
		GIT_REPOSITORY ${GIT_REPOSITORY_CHAMELEON}
		CONFIGURE_COMMAND echo
		BUILD_COMMAND echo
		INSTALL_COMMAND mkdir -p ${CMAKE_INSTALL_PREFIX}/bin
		INSTALL_COMMAND && cp -a ../chameleon/chameleon.py ${CMAKE_INSTALL_PREFIX}/bin/chameleon
	)
endif ()

if (BUILD_URCHEON)
	ExternalProject_Add(urcheon
		PREFIX ExternalProjects/urcheon
		GIT_REPOSITORY ${GIT_REPOSITORY_URCHEON}
		CONFIGURE_COMMAND echo
		BUILD_COMMAND echo
		INSTALL_COMMAND mkdir -p ${CMAKE_INSTALL_PREFIX}/bin
		INSTALL_COMMAND && mkdir -p ${CMAKE_INSTALL_PREFIX}/lib/python3/dist-packages/Urcheon
		INSTALL_COMMAND && mkdir -p ${CMAKE_INSTALL_PREFIX}/share/Urcheon/profile
		INSTALL_COMMAND && cp -a ../urcheon/Urcheon/. ${CMAKE_INSTALL_PREFIX}/lib/python3/dist-packages/Urcheon/.
		INSTALL_COMMAND && cp -a ../urcheon/profile/. ${CMAKE_INSTALL_PREFIX}/share/Urcheon/profile/.
		INSTALL_COMMAND && cp -a ../urcheon/bin/urcheon ${CMAKE_INSTALL_PREFIX}/bin/urcheon
		INSTALL_COMMAND && cp -a ../urcheon/bin/esquirel ${CMAKE_INSTALL_PREFIX}/bin/esquirel
	)
endif ()

if (BUILD_IQMTOOL)
	ExternalProject_Add(iqmtool
		PREFIX ExternalProjects/fteqw
		GIT_REPOSITORY ${GIT_REPOSITORY_IQMTOOL}
		CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
		CMAKE_ARGS -DFTE_ENGINE=OFF
		CMAKE_ARGS -DFTE_ENGINE_CLIENT_ONLY=OFF
		CMAKE_ARGS -DFTE_ENGINE_SERVER_ONLY=OFF
		CMAKE_ARGS -DFTE_CSADDON=OFF
		CMAKE_ARGS -DFTE_MENU_SYS=OFF
		CMAKE_ARGS -DFTE_PLUG_BULLET=OFF
		CMAKE_ARGS -DFTE_PLUG_CEF=OFF
		CMAKE_ARGS -DFTE_PLUG_EZHUD=OFF
		CMAKE_ARGS -DFTE_PLUG_FFMPEG=OFF
		CMAKE_ARGS -DFTE_PLUG_HL2=OFF
		CMAKE_ARGS -DFTE_PLUG_IRC=OFF
		CMAKE_ARGS -DFTE_PLUG_MODEL=OFF
		CMAKE_ARGS -DFTE_PLUG_MPQ=OFF
		CMAKE_ARGS -DFTE_PLUG_NAMEMAKER=OFF
		CMAKE_ARGS -DFTE_PLUG_ODE=OFF
		CMAKE_ARGS -DFTE_PLUG_OPENSSL=OFF
		CMAKE_ARGS -DFTE_PLUG_OPENXR=OFF
		CMAKE_ARGS -DFTE_PLUG_QI=OFF
		CMAKE_ARGS -DFTE_PLUG_QUAKE3=OFF
		CMAKE_ARGS -DFTE_PLUG_TERRAINGEN=OFF
		CMAKE_ARGS -DFTE_PLUG_TIMIDITY=OFF
		CMAKE_ARGS -DFTE_PLUG_X11SV=OFF
		CMAKE_ARGS -DFTE_PLUG_XMPP=OFF
		CMAKE_ARGS -DFTE_PLUG_MODELS=OFF
		CMAKE_ARGS -DFTE_TOOL_HTTPSV=OFF
		CMAKE_ARGS -DFTE_TOOL_IMAGE=OFF
		CMAKE_ARGS -DFTE_TOOL_IQM=ON
		CMAKE_ARGS -DFTE_TOOL_MASTER=OFF
		CMAKE_ARGS -DFTE_TOOL_QCC=OFF
		CMAKE_ARGS -DFTE_TOOL_QCCGUI=OFF
		CMAKE_ARGS -DFTE_TOOL_QTV=OFF
		INSTALL_COMMAND mkdir -p ${CMAKE_INSTALL_PREFIX}/bin
		INSTALL_COMMAND && cp -a iqmtool ${CMAKE_INSTALL_PREFIX}/bin/iqmtool
	)
endif ()

if (BUILD_CWEBP)
	ExternalProject_Add(libwebp
		PREFIX ExternalProjects/libwebp
		GIT_REPOSITORY ${GIT_REPOSITORY_CWEBP}
		GIT_TAG origin/main
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
endif ()

# opusenc and oggenc (vorbis) detects it as build time
# to enable conversion from FLAC format.
if (BUILD_FLAC OR BUILD_OPUSENC OR BUILD_OGGENC)
	ExternalProject_Add(ogg
		PREFIX ExternalProjects/ogg
		GIT_REPOSITORY ${GIT_REPOSITORY_OGG}
		BUILD_IN_SOURCE ON
		CONFIGURE_COMMAND ./autogen.sh
		CONFIGURE_COMMAND && ./configure --prefix=${CMAKE_INSTALL_PREFIX}
		BUILD_COMMAND $(MAKE)
		INSTALL_COMMAND $(MAKE) install
	)
endif ()

if (BUILD_FLAC OR BUILD_OPUSENC OR BUILD_OGGENC)
	ExternalProject_Add(flac
		DEPENDS ogg
		PREFIX ExternalProjects/flac
		GIT_REPOSITORY ${GIT_REPOSITORY_FLAC}
		BUILD_IN_SOURCE ON
		CONFIGURE_COMMAND ./autogen.sh
		CONFIGURE_COMMAND && ./configure --prefix=${CMAKE_INSTALL_PREFIX}
		BUILD_COMMAND $(MAKE)
		INSTALL_COMMAND $(MAKE) install
	)
endif ()

if (BUILD_OGGENC)
	ExternalProject_Add(vorbis
		PREFIX ExternalProjects/vorbis
		GIT_REPOSITORY ${GIT_REPOSITORY_VORBIS}
		BUILD_IN_SOURCE ON
		CONFIGURE_COMMAND ./autogen.sh
		CONFIGURE_COMMAND && ./configure --prefix=${CMAKE_INSTALL_PREFIX}
		BUILD_COMMAND $(MAKE)
		INSTALL_COMMAND $(MAKE) install
	)
endif ()

if (BUILD_OGGENC)
	ExternalProject_Add(vorbis-tools
		DEPENDS vorbis ogg
		PREFIX ExternalProjects/vorbis-tools
		GIT_REPOSITORY ${GIT_REPOSITORY_VORBISTOOLS}
		BUILD_IN_SOURCE ON
		CONFIGURE_COMMAND ./autogen.sh
		CONFIGURE_COMMAND && PKG_CONFIG_PATH=${CMAKE_INSTALL_PREFIX}/lib/pkgconfig
		CONFIGURE_COMMAND ./configure --prefix=${CMAKE_INSTALL_PREFIX}
		CONFIGURE_COMMAND --disable-vcut --disable-ogg123
		BUILD_COMMAND $(MAKE)
		INSTALL_COMMAND $(MAKE) install
	)
endif ()

if (BUILD_OPUSENC)
	ExternalProject_Add(opus
		PREFIX ExternalProjects/opus
		GIT_REPOSITORY ${GIT_REPOSITORY_OPUS}
		BUILD_IN_SOURCE ON
		CONFIGURE_COMMAND ./autogen.sh
		CONFIGURE_COMMAND && ./configure --prefix=${CMAKE_INSTALL_PREFIX}
		BUILD_COMMAND $(MAKE)
		INSTALL_COMMAND $(MAKE) install
	)
endif ()

if (BUILD_OPUSENC)
	ExternalProject_Add(opusfile
		DEPENDS opus ogg
		PREFIX ExternalProjects/opusfile
		GIT_REPOSITORY ${GIT_REPOSITORY_OPUSFILE}
		BUILD_IN_SOURCE ON
		CONFIGURE_COMMAND ./autogen.sh
		CONFIGURE_COMMAND && ./configure --prefix=${CMAKE_INSTALL_PREFIX}
		BUILD_COMMAND $(MAKE)
		INSTALL_COMMAND $(MAKE) install
	)
endif ()

if (BUILD_OPUSENC)
	ExternalProject_Add(libopusenc
		DEPENDS opus
		PREFIX ExternalProjects/libopusenc
		GIT_REPOSITORY ${GIT_REPOSITORY_OPUSENC}
		BUILD_IN_SOURCE ON
		CONFIGURE_COMMAND ./autogen.sh
		CONFIGURE_COMMAND && ./configure --prefix=${CMAKE_INSTALL_PREFIX}
		BUILD_COMMAND $(MAKE)
		INSTALL_COMMAND $(MAKE) install
	)
endif ()

if (BUILD_OPUSENC)
	ExternalProject_Add(opus-tools
		DEPENDS libopusenc opusfile opus flac ogg
		PREFIX ExternalProjects/opus-tools
		GIT_REPOSITORY ${GIT_REPOSITORY_OPUSTOOLS}
		BUILD_IN_SOURCE ON
		CONFIGURE_COMMAND ./autogen.sh
		CONFIGURE_COMMAND && PKG_CONFIG_PATH=${CMAKE_INSTALL_PREFIX}/lib/pkgconfig
		CONFIGURE_COMMAND ./configure --prefix=${CMAKE_INSTALL_PREFIX}
		BUILD_COMMAND $(MAKE)
		INSTALL_COMMAND $(MAKE) install
	)
endif ()
