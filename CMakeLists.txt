cmake_minimum_required(VERSION 3.30)

# The toolchain file setup should ideally be in a CMakePresets.json file,
# but we'll keep it here for simplicity
set(CMAKE_TOOLCHAIN_FILE "${CMAKE_CURRENT_SOURCE_DIR}/vcpkg/scripts/buildsystems/vcpkg.cmake"
    CACHE STRING "Vcpkg toolchain file")

project(GamedevPlayground VERSION 1.0)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# Add dependencies
find_package(SDL3 CONFIG REQUIRED)
find_package(GLEW CONFIG REQUIRED)

# No C++20 modules
set(CMAKE_CXX_SCAN_FOR_MODULES OFF) 

# Playground executable
add_executable(Playground)

# Playground sources and headers
file(GLOB_RECURSE Playground_SOURCES CONFIGURE_DEPENDS 
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.hpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.inl"
)

target_sources(Playground PRIVATE ${Playground_SOURCES})

# Playground include dirs
target_include_directories(Playground PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/src
)

# Preprocessor defines
target_compile_definitions(Playground PRIVATE
    MY_TEST_DEFINE
)

# Automatically links to these libs
target_link_libraries(Playground PRIVATE
    SDL3::SDL3
    GLEW::GLEW
)

# Add directories with any random loose .dlls or .libs here
target_link_directories(Playground PRIVATE
  
)

# Set configuration properties for Playground
if(MSVC)
    target_compile_options(Playground PRIVATE
        /W3
        /MP # multithreaded build
        /WX # warnings as errors
        /ZI # program database for edit and continue
    )
else()
    target_compile_options(Playground PRIVATE 
        -Wall 
        -Wextra 
        -Wpedantic
    )
endif()

# Set Playground as the startup project
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT Playground)

# Filters
source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}/src" PREFIX "Source Files" FILES ${Playground_SOURCES})

# Enable solution folders
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
