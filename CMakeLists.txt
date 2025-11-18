cmake_minimum_required(VERSION 3.20)

set(T c_opengl_template)

project(${T} LANGUAGES C)

find_package(OpenGL REQUIRED)

set(CMAKE_C_STANDARD 11)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(EXTERNAL_DIR ${CMAKE_CURRENT_SOURCE_DIR}/external)
set(OGL_UTIL_DIR ${CMAKE_CURRENT_SOURCE_DIR}/util)
set(GLFW_DIR ${EXTERNAL_DIR}/glfw-3.4)
set(GLAD_DIR ${EXTERNAL_DIR}/glad)
set(CGLM_DIR ${EXTERNAL_DIR}/cglm-0.9.6)
set(STB_IMAGE_DIR ${EXTERNAL_DIR}/stb_image)

add_subdirectory(${GLFW_DIR} EXCLUDE_FROM_ALL)
add_subdirectory(${CGLM_DIR} EXCLUDE_FROM_ALL)
add_subdirectory(${GLAD_DIR} EXCLUDE_FROM_ALL)
add_subdirectory(${OGL_UTIL_DIR} EXCLUDE_FROM_ALL)

add_executable(${T} main.c)
target_compile_definitions(${T} PRIVATE GLFW_INCLUDE_NONE)
target_include_directories(${T} PRIVATE ${STB_IMAGE_DIR})
target_link_libraries(${T} PRIVATE glad glfw OpenGL::GL cglm m ogl_util)
target_compile_options(${T} PRIVATE -Wall -Wextra -Wpedantic)

add_custom_target(copy_shaders ALL
        COMMAND ${CMAKE_COMMAND} -E make_directory "$<TARGET_FILE_DIR:${T}>/shaders"
        COMMAND ${CMAKE_COMMAND} -E copy_directory
        "${CMAKE_CURRENT_SOURCE_DIR}/shaders"
        "$<TARGET_FILE_DIR:${T}>/shaders"
)

add_dependencies(${T} copy_shaders)
