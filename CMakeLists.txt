cmake_minimum_required(VERSION 3.5.0)
project(pacman VERSION 0.1.0 LANGUAGES C)

include(CTest)
enable_testing()

# Specify the path to the include and lib directories relative to the CMakeLists.txt file.
# This will only be used on Windows, where you typically include the SDL2 binaries with your project.

if(WIN32)
    set(SDL2_INCLUDE_DIR "${CMAKE_SOURCE_DIR}/libs/SDL2-2.30.1/include")
    set(SDL2_LIBRARY "${CMAKE_SOURCE_DIR}/libs/SDL2-2.30.1/lib/x64/SDL2.lib")
    set(SDL2MAIN_LIBRARY "${CMAKE_SOURCE_DIR}/libs/SDL2-2.30.1/lib/x64/SDL2main.lib")
endif()

# Add the executable
add_executable(pacman main.c)

if(WIN32)
    # Include the SDL2 headers and link the libraries for Windows
    target_include_directories(pacman PRIVATE ${SDL2_INCLUDE_DIR})
    target_link_libraries(pacman PRIVATE ${SDL2_LIBRARY} ${SDL2MAIN_LIBRARY})

    # Post-build, copy SDL2.dll to the same directory as the executable
    add_custom_command(TARGET pacman POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        "${CMAKE_SOURCE_DIR}/libs/SDL2-2.30.1/lib/x64/SDL2.dll"
        $<TARGET_FILE_DIR:pacman>)
elseif(UNIX)
    # Include the SDL2 headers and link the libraries for Linux
    find_package(SDL2 REQUIRED)
    target_include_directories(pacman PRIVATE ${SDL2_INCLUDE_DIRS})
    target_link_libraries(pacman PRIVATE ${SDL2_LIBRARIES})
endif()

set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)