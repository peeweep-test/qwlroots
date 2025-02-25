set(TARGET tinywl)
set(CMAKE_INCLUDE_CURRENT_DIR ON)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Gui REQUIRED)

set(SOURCES main.cpp)

find_package(PkgConfig)
pkg_search_module(WAYLAND_SERVER REQUIRED IMPORTED_TARGET wayland-server)
pkg_search_module(WLROOTS REQUIRED IMPORTED_TARGET wlroots)
pkg_search_module(PIXMAN REQUIRED IMPORTED_TARGET pixman-1)
pkg_search_module(XKBCOMMON REQUIRED IMPORTED_TARGET xkbcommon)

setup_package_version_variables(WLROOTS)
add_definitions(-DWLR_VERSION_STRING="${WLROOTS_VERSION}"
                -DWLR_VERSION_MAJOR=${WLROOTS_VERSION_MAJOR}
                -DWLR_VERSION_MINOR=${WLROOTS_VERSION_MINOR}
                -DWLR_VERSION_PATCH=${WLROOTS_VERSION_PATCH})

include(${PROJECT_SOURCE_DIR}/cmake/WaylandScannerHelpers.cmake)
ws_generate(server stable/xdg-shell/xdg-shell.xml xdg-shell-protocol)

add_executable(${TARGET}
    ${SOURCES}
)

target_link_libraries(${TARGET}
    PRIVATE
    Qt${QT_VERSION_MAJOR}::Gui
    qwlroots
    PkgConfig::WAYLAND_SERVER
    PkgConfig::WLROOTS
    PkgConfig::PIXMAN
    PkgConfig::XKBCOMMON
)

target_include_directories(${TARGET}
    PRIVATE
    ${Qt${QT_VERSION_MAJOR}Gui_PRIVATE_INCLUDE_DIRS}
)
