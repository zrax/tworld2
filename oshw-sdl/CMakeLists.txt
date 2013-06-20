find_package(SDL REQUIRED)
include_directories(${SDL_INCLUDE_DIR} "${CMAKE_CURRENT_SOURCE_DIR}")

set(OSHW_SDL_SOURCES
    ${tworld2_SOURCE_DIR}/generic/in.c
    ${tworld2_SOURCE_DIR}/generic/generic.c
    ${tworld2_SOURCE_DIR}/generic/tile.c
    ${tworld2_SOURCE_DIR}/generic/timer.c

    oshwbind.c sdloshw.c sdlerr.c sdlin.c sdlout.c sdltext.c sdlsfx.c
)

add_library(oshw-sdl STATIC ${OSHW_SDL_SOURCES})
target_link_libraries(oshw-sdl ${SDL_LIBRARY})
