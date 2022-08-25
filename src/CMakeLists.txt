cmake_minimum_required( VERSION 3.23 )

# Information needed for CMake's 'project' command.
set( objconv_homepage "https://www.agner.org/optimize/" )
string( APPEND objconv_description
    "A utility for cross-platform development of function libraries, for converting "
    "and modifying object files and for dumping and disassembling object and "
    " executable files for all x86 and x86-64 platforms."
)
# Parse current version number.
file( STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/maindef.h" objconv_version REGEX "^.*OBJCONV_VERSION.*$" )
string( REGEX REPLACE "^.*[ \t]+([0-9.]+).*$" "\\1" objconv_version "${objconv_version}" )

project( objconv
    VERSION      "${objconv_version}"
    DESCRIPTION  "${objconv_description}"
    HOMEPAGE_URL "${objconv_homepage}"
    LANGUAGES    CXX
)

# By default hide all symbols and build position-independent code.
set( CMAKE_CXX_VISIBILITY_PRESET     hidden )
set( CMAKE_VISIBILITY_INLINES_HIDDEN ON )
set( CMAKE_POSITION_INDEPENDENT_CODE ON )

# Create objconv executable.
add_executable( objconv-executable )
add_executable( objconv::objconv ALIAS objconv-executable )
target_sources( objconv-executable
    PRIVATE
        main.cpp
        error.cpp
        containers.cpp
        cmdline.cpp
        library.cpp

        opcodes.cpp
        disasm1.cpp
        disasm2.cpp

        elf.cpp
        elf2asm.cpp
        elf2cof.cpp
        elf2elf.cpp
        elf2mac.cpp

        macho.cpp
        mac2asm.cpp
        mac2elf.cpp
        mac2mac.cpp

        coff.cpp
        cof2asm.cpp
        cof2cof.cpp
        cof2elf.cpp
        cof2omf.cpp

        omf.cpp
        omfhash.cpp
        omf2asm.cpp
        omf2cof.cpp

    PRIVATE FILE_SET HEADERS
        BASE_DIRS "${PROJECT_SOURCE_DIR}"
        FILES
            stdafx.h
            maindef.h
            error.h
            containers.h
            converters.h
            cmdline.h
            library.h
            disasm.h
            elf.h
            macho.h
            coff.h
            omf.h
)
# Calculate the PROJECT_LABEL property for proper displaying in IDEs.
set( unicodeProportionChar "∷" )  # A fixed double-colon (U+2237) that does not collide with the drive-separator on Windows systems.
string( REPLACE "::" "${unicodeProportionChar}" project_label "objconv::objconv" )
# Set executable-name, version and display-name for IDEs.
set_target_properties( objconv-executable PROPERTIES
    OUTPUT_NAME objconv
    OUTPUT_NAME_DEBUG objconv_dbg
    VERSION ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    PROJECT_LABEL "${project_label}"
)
