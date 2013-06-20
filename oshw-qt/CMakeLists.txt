find_package(Qt4 COMPONENTS QtCore QtGui QtXml QtMain REQUIRED)
include(${QT_USE_FILE})
include_directories(${QT_INCLUDES} "${CMAKE_CURRENT_SOURCE_DIR}"
                    "${CMAKE_CURRENT_BINARY_DIR}")

# The Qt4 OSHW requires SDL for sound :(
find_package(SDL REQUIRED)
include_directories(${SDL_INCLUDE_DIR} "${tworld2_SOURCE_DIR}/oshw-sdl")

set(OSHW_QT_SOURCES
    ${tworld2_SOURCE_DIR}/generic/_in.cpp
    ${tworld2_SOURCE_DIR}/generic/generic.c
    ${tworld2_SOURCE_DIR}/generic/tile.c
    ${tworld2_SOURCE_DIR}/generic/timer.c

    _sdlsfx.c oshwbind.cpp CCMetaData.cpp TWApp.cpp TWDisplayWidget.cpp
    TWMainWnd.cpp TWProgressBar.cpp
)

set(OSHW_QT_MOCS
    oshwbind.h CCMetaData.h TWApp.h TWDisplayWidget.h TWMainWnd.h
    TWProgressBar.h
)

set(OSHW_QT_UIS
    TWMainWnd.ui
)

qt4_wrap_cpp(OSHW_QT_MOCSRC ${OSHW_QT_MOCS})
qt4_wrap_ui(OSHW_QT_UISRC ${OSHW_QT_UIS})
add_library(oshw-qt STATIC ${OSHW_QT_SOURCES}
             ${OSHW_QT_MOCSRC} ${OSHW_QT_UISRC})
target_link_libraries(oshw-qt ${QT_LIBRARIES} ${SDL_LIBRARY})
