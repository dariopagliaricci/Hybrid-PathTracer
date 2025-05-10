cmake_minimum_required(VERSION 3.15)
project(PathTracer)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Glob source files
file(GLOB_RECURSE SRC_FILES src/*.cpp src/*.h)
file(GLOB_RECURSE SHADER_FILES src/shaders/*.glsl)
file(GLOB_RECURSE EXT_FILES
    thirdparty/imgui/*.cpp
    thirdparty/imgui/*.h
    thirdparty/imguizmo/*.cpp
    thirdparty/imguizmo/*.h
    thirdparty/stb/*.h
    thirdparty/tinyobjloader/*.h
    thirdparty/tinygltf/*.h
    thirdparty/tinydir/*.h
)

# Add Apple-specific GL loader
list(APPEND EXT_FILES thirdparty/gl3wApple/GL/gl3w.c)

# Add RadeonRays implementation files
list(APPEND EXT_FILES
    ${CMAKE_SOURCE_DIR}/thirdparty/RadeonRays/bvh.cpp
    ${CMAKE_SOURCE_DIR}/thirdparty/RadeonRays/split_bvh.cpp
    ${CMAKE_SOURCE_DIR}/thirdparty/RadeonRays/bvh_translator.cpp
    ${CMAKE_SOURCE_DIR}/thirdparty/RadeonRays/bbox.cpp
)

set(SOURCES ${SRC_FILES} ${EXT_FILES} ${SHADER_FILES})

# Find and link dependencies from Homebrew (or system paths)
find_package(OpenGL REQUIRED)
find_package(SDL2 REQUIRED)

include_directories(
    /opt/homebrew/include
    ${SDL2_INCLUDE_DIRS}
    src/core
    src/loaders
    src/math
    src/tests
    thirdparty/imgui
    thirdparty/imguizmo
    thirdparty/stb
    thirdparty/tinygltf
    thirdparty/tinydir
    thirdparty/tinyobjloader
    thirdparty/oidn/include
    thirdparty/RadeonRays
    thirdparty/gl3wApple
)

link_directories(/opt/homebrew/lib)

add_executable(PathTracer ${SOURCES})

target_link_libraries(PathTracer
    ${OPENGL_LIBRARIES}
    SDL2
    OpenImageDenoise
    dl
)

# Definitions
add_definitions(
    -D_CRT_SECURE_NO_WARNINGS
    -D__STDC_FORMAT_MACROS
    -D__STDC_LIMIT_MACROS
    -D__STDC_CONSTANT_MACROS
    -DIMGUI_DISABLE_OBSOLETE_FUNCTIONS
    -DUSE_DL_PREFIX
    -DGLEW_STATIC
)

# Output dirs
set_target_properties(PathTracer PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY_DEBUG       ${CMAKE_CURRENT_BINARY_DIR}
    RUNTIME_OUTPUT_DIRECTORY_RELEASE     ${CMAKE_CURRENT_BINARY_DIR}
    RUNTIME_OUTPUT_DIRECTORY_RELWITHDEBINFO ${CMAKE_CURRENT_BINARY_DIR}
    DEBUG_POSTFIX "_d"
)