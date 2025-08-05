cmake_minimum_required(VERSION 3.5...4.0)

if (NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    message(STATUS "No build type selected, default to Debug")
    set(CMAKE_BUILD_TYPE "Debug")
endif()

project(binkstub VERSION 1.0.0 LANGUAGES C)

add_library(binkstub SHARED bink.c)
if(MINGW)
    target_sources(binkstub PRIVATE bink_mingw.def)
endif()
if(WIN32 AND CMAKE_SIZEOF_VOID_P EQUAL 4)
    if(MINGW)
        target_sources(binkstub PRIVATE bink_mingw.def)
    endif()
endif()
set_target_properties(binkstub PROPERTIES DEFINE_SYMBOL "BINKDLL")
set_target_properties(binkstub PROPERTIES OUTPUT_NAME binkw32)
set_target_properties(binkstub PROPERTIES SOVERSION 1.0 VERSION 1.0.0)
target_include_directories(binkstub PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
)
if(WIN32)
    set_target_properties(binkstub PROPERTIES PREFIX "")
endif()
