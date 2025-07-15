cmake_minimum_required(VERSION 3.1)
project(tray VERSION 0.2 DESCRIPTION "A cross-platform C++ system tray library")

file(GLOB_RECURSE src
    "tray/src/*.cpp"
)

add_library(tray STATIC ${src})

# Set C++ standard
target_compile_features(tray PUBLIC cxx_std_17)
set_target_properties(tray PROPERTIES 
                      CXX_STANDARD 17
                      CXX_EXTENSIONS OFF
                      CXX_STANDARD_REQUIRED ON)

# Include directories
target_include_directories(tray PUBLIC "tray/include")

# Platform-specific configuration
if(UNIX)
    find_package(PkgConfig REQUIRED)
    pkg_check_modules(GTK3 REQUIRED gtk+-3.0)
    pkg_check_modules(APPINDICATOR REQUIRED ayatana-appindicator3-0.1)

    # Debug: Print the found paths
    message(STATUS "GTK3_INCLUDE_DIRS: ${GTK3_INCLUDE_DIRS}")
    message(STATUS "APPINDICATOR_INCLUDE_DIRS: ${APPINDICATOR_INCLUDE_DIRS}")

    target_link_libraries(tray PUBLIC ${GTK3_LIBRARIES} ${APPINDICATOR_LIBRARIES})
    
    # Add include directories without SYSTEM to ensure they're visible
    target_include_directories(tray PUBLIC ${GTK3_INCLUDE_DIRS} ${APPINDICATOR_INCLUDE_DIRS})

    if(CMAKE_BUILD_TYPE STREQUAL "Debug")
        target_compile_options(tray PRIVATE -Wall -Wextra -Werror -pedantic -Wno-unused-lambda-capture)
    endif()
    
elseif(WIN32)
    target_link_libraries(tray PUBLIC shell32 user32)
endif()

set_target_properties(tray PROPERTIES VERSION ${PROJECT_VERSION})