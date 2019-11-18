cmake_minimum_required(VERSION 3.13)
project(ge211
        VERSION 2019.1.0
        DESCRIPTION "A student game engine"
        LANGUAGES CXX)

###
### DEPENDENCIES
###

list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/Modules")
#add_subdirectory(3rdparty/utf8-cpp)

find_package(Utf8-cpp   REQUIRED)
find_package(SDL2       REQUIRED)
find_package(SDL2_image REQUIRED)
find_package(SDL2_mixer REQUIRED)
find_package(SDL2_ttf   REQUIRED)

###
### MAIN LIBRARY SETUP
###
add_library(ge211
        include/ge211.h
        include/ge211_forward.h
        include/ge211_time.h
        include/ge211_util.h

        src/ge211_base.cpp
        src/ge211_color.cpp
        src/ge211_engine.cpp
        src/ge211_event.cpp
        src/ge211_error.cpp
        src/ge211_geometry.cpp
        src/ge211_audio.cpp
        src/ge211_random.cpp
        src/ge211_render.cpp
        src/ge211_resource.cpp
        src/ge211_session.cpp
        src/ge211_sprites.cpp
        src/ge211_window.cpp)

#get_target_property( UTF8-CPP_INTERFACE_INCLUDE_DIRECTORIES utf8-cpp INTERFACE_INCLUDE_DIRECTORIES )

set_target_properties(ge211
    PROPERTIES
        CXX_STANDARD 14
        CXX_STANDARD_REQUIRED On
        CXX_EXTENSIONS Off
        VERSION ${PROJECT_VERSION}
        INTERFACE_INCLUDE_DIRECTORIES   "${SDL2_INCLUDE_DIRS}")

if(MSVC)
    target_compile_options(ge211 PRIVATE /W4)
else(MSVC)
    target_compile_options(ge211 PRIVATE -Wall -Wextra -pedantic)
endif(MSVC)

target_include_directories(ge211
    SYSTEM INTERFACE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
        ${SDL2_INCLUDE_DIRS}
        ${SDL2_IMAGE_INCLUDE_DIRS}
        ${SDL2_MIXER_INCLUDE_DIRS}
        ${SDL2_TTF_INCLUDE_DIRS})

target_include_directories(ge211
    PRIVATE
        include
        ${SDL2_INCLUDE_DIRS}
        ${SDL2_IMAGE_INCLUDE_DIRS}
        ${SDL2_MIXER_INCLUDE_DIRS}
        ${SDL2_TTF_INCLUDE_DIRS})

target_link_libraries(ge211
    PUBLIC
        ${SDL2_LIBRARIES}
        ${SDL2_IMAGE_LIBRARIES}
        ${SDL2_MIXER_LIBRARIES}
        ${SDL2_TTF_LIBRARIES}
        utf8-cpp)

SET(GE211_RESOURCE_PATH
    "${CMAKE_CURRENT_SOURCE_DIR}/Resources/"
    "${CMAKE_INSTALL_FULL_DATADIR}/ge211/")

target_compile_definitions(ge211 PRIVATE
    -DGE211_RESOURCES="$<JOIN:${GE211_RESOURCE_PATH},\"$<COMMA>\">")

###
### LIBRARY INSTALLATION
###

include(GNUInstallDirs)

install(TARGETS ge211 EXPORT Ge211Config
        ARCHIVE  DESTINATION ${CMAKE_INSTALL_LIBDIR}
        LIBRARY  DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME  DESTINATION ${CMAKE_INSTALL_BINDIR})

install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
install(DIRECTORY Resources/ DESTINATION ${CMAKE_INSTALL_DATADIR}/ge211)
install(DIRECTORY Modules/ DESTINATION ${CMAKE_ROOT}/Modules)

install(EXPORT Ge211Config
        DESTINATION ${CMAKE_INSTALL_DATADIR}/cmake/Ge211
        EXPORT_LINK_INTERFACE_LIBRARIES )
install(CODE "FILE(APPEND ${CMAKE_INSTALL_FULL_DATADIR}/cmake/Ge211/Ge211Config.cmake \"MESSAGE( \"HOLA\n\")\n\") ")

install(CODE "FILE(APPEND ${CMAKE_INSTALL_FULL_DATADIR}/cmake/Ge211/Ge211Config.cmake \"find_package(Utf8-cpp   REQUIRED)\n\") ")
install(CODE "FILE(APPEND ${CMAKE_INSTALL_FULL_DATADIR}/cmake/Ge211/Ge211Config.cmake \"find_package(SDL2   REQUIRED)\n\") ")
install(CODE "FILE(APPEND ${CMAKE_INSTALL_FULL_DATADIR}/cmake/Ge211/Ge211Config.cmake \"find_package(SDL2_image   REQUIRED)\n\") ")
install(CODE "FILE(APPEND ${CMAKE_INSTALL_FULL_DATADIR}/cmake/Ge211/Ge211Config.cmake \"find_package(SDL2_mixer   REQUIRED)\n\") ")
install(CODE "FILE(APPEND ${CMAKE_INSTALL_FULL_DATADIR}/cmake/Ge211/Ge211Config.cmake \"find_package(SDL2_ttf   REQUIRED)\n\") ")

export(TARGETS ge211 FILE Ge211Config.cmake)
