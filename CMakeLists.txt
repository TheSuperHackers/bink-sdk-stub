cmake_minimum_required(VERSION 3.5...4.0)

if (NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    message(STATUS "No build type selected, default to Debug")
    set(CMAKE_BUILD_TYPE "Debug")
endif()

project(binkstub VERSION 1.0.0 LANGUAGES C)

include(CMakePackageConfigHelpers)
include(GNUInstallDirs)

add_library(binkstub SHARED bink.c)
add_library(BinkStub::BinkStub ALIAS binkstub)
if(NOT TARGET Bink::Bink)
    add_library(Bink::Bink ALIAS binkstub)
endif()
if(WIN32 AND CMAKE_SIZEOF_VOID_P EQUAL 4)
    if(MINGW)
        target_sources(binkstub PRIVATE bink_mingw.def)
    endif()
endif()
set_target_properties(binkstub PROPERTIES DEFINE_SYMBOL "BINKDLL")
set_target_properties(binkstub PROPERTIES PUBLIC_HEADER "bink.h")
set_target_properties(binkstub PROPERTIES EXPORT_NAME "BinkStub")
set_target_properties(binkstub PROPERTIES OUTPUT_NAME binkw32)
set_target_properties(binkstub PROPERTIES SOVERSION 1.0 VERSION 1.0.0)
target_include_directories(binkstub PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)
if(WIN32)
    set_target_properties(binkstub PROPERTIES PREFIX "")
endif()

if(binkstub_IS_TOP_LEVEL)
    install(TARGETS binkstub EXPORT BinkStubExports)
    if(MSVC)
        set(cmake_install_cfgdir "cmake")
    else()
        set(cmake_install_cfgdir "${CMAKE_INSTALL_LIBDIR}/cmake/BinkStub")
    endif()
    configure_package_config_file(cmake/BinkStubConfig.cmake.in BinkStubConfig.cmake
        NO_SET_AND_CHECK_MACRO
        PATH_VARS CMAKE_INSTALL_PREFIX
        INSTALL_DESTINATION "${cmake_install_cfgdir}"
    )
    export(TARGETS binkstub NAMESPACE "BinkStub::" FILE "BinkStubTargets.cmake")
    install(EXPORT BinkStubExports
        NAMESPACE "BinkStub::"
        DESTINATION "${cmake_install_cfgdir}"
        FILE "BinkStubTargets.cmake"
    )
    install(FILES "${CMAKE_CURRENT_BINARY_DIR}/BinkStubConfig.cmake"
        DESTINATION "${cmake_install_cfgdir}"
    )
endif()
