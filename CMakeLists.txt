cmake_minimum_required(VERSION 2.6)
project(tworld2)

if(CMAKE_COMPILER_IS_GNUCC OR CMAKE_COMPILER_IS_GNUCXX)
    set(CMAKE_C_FLAGS "-Wall -Wextra ${CMAKE_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "-Wall -Wextra ${CMAKE_CXX_FLAGS}")
endif()

include_directories("${CMAKE_CURRENT_SOURCE_DIR}"
                    "${CMAKE_CURRENT_BINARY_DIR}")

if(NOT WIN32 AND NOT APPLE)
    # Standard location for *nix systems
    add_definitions("-DROOTDIR=\"${CMAKE_INSTALL_PREFIX}/share/tworld\"")
endif()

# Add the OS/HW abstraction
option(OSHW_USE_QT "Use the Qt GUI (tworld 2.0) instead of the SDL one (tworld 1.x)" ON)
if(OSHW_USE_QT)
    add_definitions("-D__TWPLUSPLUS")
    add_subdirectory(oshw-qt)
else()
    add_subdirectory(oshw-sdl)
endif()

# Core TWorld sources
set(TWorld_SOURCES
    tworld.c series.c play.c encoding.c solution.c res.c lxlogic.c mslogic.c
    unslist.c help.c score.c random.c cmdline.c fileio.c err.c comptime.h
    tworld.rc
)

# Auto-generate comptime.h
if(WIN32)
    add_custom_command(OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/comptime.h"
                       COMMAND echo \#define COMPILE_TIME \"%DATE% %TIME%\"
                               > "${CMAKE_CURRENT_BINARY_DIR}/comptime.h")
else()
    add_custom_command(OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/comptime.h"
                       COMMAND echo \\\#define COMPILE_TIME \\\"`date '+%Y %b %e %T %Z'`\\\"
                               > "${CMAKE_CURRENT_BINARY_DIR}/comptime.h")
endif()

# Tile world executable
if(APPLE)
    set(TW2_NAME "Tile World")
else()
    set(TW2_NAME tworld2)
endif()

add_executable("${TW2_NAME}" WIN32 MACOSX_BUNDLE ${TWorld_SOURCES})
if(OSHW_USE_QT)
    target_link_libraries("${TW2_NAME}" oshw-qt)
else()
    target_link_libraries("${TW2_NAME}" oshw-sdl)
endif()

# mklynxcc tool
add_executable(mklynxcc mklynxcc.c)

# Installation parameters
set(TWORLD_DATA
    "${CMAKE_CURRENT_SOURCE_DIR}/data/intro.dat"
)

set(TWORLD_SETS
    "${CMAKE_CURRENT_SOURCE_DIR}/sets/intro-lynx.dac"
    "${CMAKE_CURRENT_SOURCE_DIR}/sets/intro-ms.dac"
    "${CMAKE_CURRENT_SOURCE_DIR}/sets/cc-lynx.dac"
    "${CMAKE_CURRENT_SOURCE_DIR}/sets/cc-ms.dac"
    "${CMAKE_CURRENT_SOURCE_DIR}/sets/CCLP2.dac"
)

set(TWORLD_RES
    "${CMAKE_CURRENT_SOURCE_DIR}/res/rc"

    "${CMAKE_CURRENT_SOURCE_DIR}/res/atiles.bmp"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/tiles.bmp"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/font.bmp"

    "${CMAKE_CURRENT_SOURCE_DIR}/res/block.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/bomb.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/bump.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/chack.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/click.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/crackle.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/death.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/derezz.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/ding.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/door.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/force.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/oof.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/plip.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/popup.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/skate.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/skaturn.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/slurp.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/snick.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/socket.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/splash.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/tada.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/teleport.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/thief.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/tick.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/ting.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/traphit.wav"
    "${CMAKE_CURRENT_SOURCE_DIR}/res/whisk.wav"

    "${CMAKE_CURRENT_SOURCE_DIR}/res/unslist.txt"
)

if(APPLE)
    set(MACOSX_BUNDLE true)
    set(MACOSX_BUNDLE_NAME "${TW2_NAME}")
    set(MACOSX_BUNDLE_INFO_STRING "Tile World 2.0")
    set(MACOSX_BUNDLE_LONG_VERSION_STRING "2.0.0")
    set(MACOSX_BUNDLE_SHORT_VERSION_STRING "2.0.0")
    set(MACOSX_BUNDLE_COPYRIGHT "Copyright (C) 2001-2010 by Brian Raiter and Madhav Shanbhag")
    set(MACOSX_BUNDLE_ICON_FILE "tworld.icns")

    install(TARGETS "${TW2_NAME}"
            BUNDLE DESTINATION .
    )
    install(FILES "${CMAKE_CURRENT_SOURCE_DIR}/tworld.icns"
            DESTINATION "${TW2_NAME}.app/Contents/Resources"
    )
    install(TARGETS mklynxcc
            DESTINATION .
    )
elseif(WIN32)
    install(TARGETS "${TW2_NAME}" mklynxcc
            RUNTIME DESTINATION .
    )
else()
    install(TARGETS "${TW2_NAME}" mklynxcc
            RUNTIME DESTINATION bin
    )
    install(FILES "${CMAKE_CURRENT_SOURCE_DIR}/docs/tworld2.6"
            DESTINATION share/man
    )
    install(FILES ${TWORLD_DATA}
            DESTINATION share/tworld/data
    )
    install(FILES ${TWORLD_SETS}
            DESTINATION share/tworld/sets
    )
    install(FILES ${TWORLD_RES}
            DESTINATION share/tworld/res
    )
endif()
