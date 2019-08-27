cmake_minimum_required(VERSION 2.8.12)

cmake_policy(SET CMP0048 NEW) # To get package versioning working
project(FSON VERSION 1.0.0 LANGUAGES Fortran C)

set(CMAKE_MACOSX_RPATH 1)

if (NOT FSON_DIR)
    include(FetchContent OPTIONAL RESULT_VARIABLE fc_LOADED)
    if (${fc_LOADED} MATCHES NOTFOUND)
        include(${CMAKE_CURRENT_SOURCE_DIR}/cmake/FetchContent.cmake)
    endif()
    FetchContent_Declare(fson_proj
        GIT_REPOSITORY https://github.com/josephalevin/fson.git
    )
    FetchContent_GetProperties(fson_proj)
    if (NOT fson_proj_POPULATED)
        FetchContent_Populate(fson_proj)
    endif()
    set(FSON_DIR ${fson_proj_SOURCE_DIR})
endif()

add_library(FSON SHARED
    ${FSON_DIR}/src/fson_string_m.f90
    ${FSON_DIR}/src/fson_value_m.f90
    ${FSON_DIR}/src/fson_path_m.f90
    ${FSON_DIR}/src/fson.f90
)

add_library(FSON::FSON ALIAS FSON)

target_include_directories(FSON
    PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}
)

target_compile_options(FSON
    PUBLIC
    PRIVATE
    $<$<OR:$<Fortran_COMPILER_ID:GNU>,$<Fortran_COMPILER_ID:G95>>:
        -Wno-maybe-uninitialized
    >
)

#
# Installation details
#
include(GNUInstallDirs)
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/fson)

install(TARGETS FSON
    EXPORT fson-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

# Add the correct module for `use` here.
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/fson.mod
    ${CMAKE_CURRENT_BINARY_DIR}/fson_path_m.mod
    ${CMAKE_CURRENT_BINARY_DIR}/fson_value_m.mod
    ${CMAKE_CURRENT_BINARY_DIR}/fson_string_m.mod
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

install(EXPORT fson-targets
    FILE
        FSONTargets.cmake
    NAMESPACE
        FSON::
    DESTINATION
        ${INSTALL_CONFIGDIR}
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/FSONConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(
    ${CMAKE_CURRENT_LIST_DIR}/cmake/FSONConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/FSONConfig.cmake
    INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
)

install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/FSONConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/FSONConfigVersion.cmake
    DESTINATION ${INSTALL_CONFIGDIR}
)

#
# Export for testing
#
export(EXPORT fson-targets
    FILE
        ${CMAKE_CURRENT_BINARY_DIR}/FSONTargets.cmake
    NAMESPACE
        FSON::
)

# Register the package in the user's registry
export(PACKAGE FSON)

