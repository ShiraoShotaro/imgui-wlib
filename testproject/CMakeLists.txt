
include(${CMAKE_BINARY_DIR}/conan_paths.cmake)
find_package(GLEW   2.2.0 REQUIRED)
find_package(glfw3  3.3.5 REQUIRED)
find_package(OpenGL       REQUIRED)

add_executable(testproject
    main.cpp

    ../backends/imgui_impl_opengl3.cpp
    ../backends/imgui_impl_glfw.cpp
)

target_include_directories(testproject
    PRIVATE
        ../backends
)

target_link_libraries(testproject
    PRIVATE
        GLEW::GLEW
        glfw::glfw
        opengl::opengl
        imgui-wlib
)