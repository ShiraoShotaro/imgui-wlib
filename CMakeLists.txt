# Copyright (C) 2021 Shotaro Shirao
# Distributed under the MIT License, see http://opensource.org/licenses/MIT.

cmake_minimum_required(VERSION 3.22)

project(
    imgui-wlib
        VERSION
            0.1.0
        DESCRIPTION
            "imgui with c++"
        LANGUAGES
            CXX
)

add_compile_options(
    "$<$<CXX_COMPILER_ID:MSVC>:/utf-8>"
    "$<$<CXX_COMPILER_ID:MSVC>:/EHa>"
    "$<$<CXX_COMPILER_ID:MSVC>:/EHsc>"
    "$<$<CXX_COMPILER_ID:MSVC>:/MP>"
)

set(WLIB_IMGUI_SOURCE_FILES
    # -- imgui --
    src/imgui/imgui_demo.cpp
    src/imgui/imgui_draw.cpp
    src/imgui/imgui_tables.cpp
    src/imgui/imgui_widgets.cpp
    src/imgui/imgui.cpp

    # -- widgets --
)

set(WLIB_IMGUI_HEADER_FILES
    # -- imgui --
    include/imgui/imconfig.h
    include/imgui/imgui_internal.h
    include/imgui/imgui.h
    include/imgui/imstb_rectpack.h
    include/imgui/imstb_textedit.h
    include/imgui/imstb_truetype.h

    # -- widgets --
)

add_library(imgui-wlib
    ${WLIB_IMGUI_SOURCE_FILES}
    ${WLIB_IMGUI_HEADER_FILES}
)

target_include_directories(imgui-wlib
    PUBLIC
        include
)

set_target_properties(imgui-wlib
    PROPERTIES
        CXX_STANDARD
            17
        PUBLIC_HEADER
            "${WLIB_IMGUI_HEADER_FILES}"
)

add_subdirectory(testproject)

function(wlib_add_source_vsfilter TARGET_NAME)
    get_target_property(_SRCFILES
        ${TARGET_NAME}
            SOURCES
    )
    foreach(__source IN ITEMS ${_SRCFILES})
    if (IS_ABSOLUTE "${__source}")
        file(RELATIVE_PATH _source_rel "${CMAKE_CURRENT_SOURCE_DIR}" "${__source}")
    else()
        set(_source_rel "${__source}")
    endif()
    get_filename_component(_source_path "${_source_rel}" DIRECTORY)
    string(REPLACE "/" "\\" __source_path_msvc "${_source_path}")

    get_filename_component(_source_ext "${_source_rel}" EXT)
    if (_source_ext STREQUAL ".cpp")
        string(REPLACE "src" "src" _source_path_msvc "${__source_path_msvc}")
    elseif(_source_ext STREQUAL ".hpp")
        string(REPLACE "include" "src" _source_path_msvc "${__source_path_msvc}")
    elseif(_source_ext STREQUAL ".h")
        string(REPLACE "include" "src" _source_path_msvc "${__source_path_msvc}")
    else()
        message(FATAL_ERROR "Unknown file extension. ${_source_rel}")
    endif()

    source_group("${_source_path_msvc}" FILES "${__source}")
    endforeach()
endfunction(wlib_add_source_vsfilter)

wlib_add_source_vsfilter(imgui-wlib)