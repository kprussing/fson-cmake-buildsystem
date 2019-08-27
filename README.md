CMake Build System for FSON
===========================

This provides a CMake build system for the [FSON][FSON] project.  It
exploits the full functionality of CMake and exports the FSON library as
the `FSON::FSON` target.

Installation
------------

This is simply a build configuration for FSON and does not manage the
source.  As such, you can use this in two different ways.  First, to
just get FSON and configure, simply use

    mkdir bld
    cd bld
    cmake ..

with all the normal CMake options.  This will clone the FSON repository
and handle generating then designated build system files.  This does
require `git` 1.6.5 or newer to be available to CMake.

Alternatively, if you already have a copy of the FSON source tree simply
set the `FSON_DIR` variable via

    mkdir bld
    cd bld
    cmake -DFSON_DIR=/path/to/your/fson ..

Usage
-----

Once FSON has been installed, simply add

    find_package(FSON)

    target_link_libraries(mytarget FSON::FSON)

to your `CMakeLists.txt`

License
-------

This project is licensed in accordance with the original FSON project.

[FSON]: https://github.com/josephalevin/fson

