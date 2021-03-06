# Macro to add a build target for an IO plugin.
#
# Note: the original is in OIIO's src/cmake/oiio_macros.cmake
#
# Usage:
#
# add_oiio_plugin ( source1 [source2 ...]
#                   [LINK_LIBRARIES external_lib1 ...] )
#
# The plugin name is deduced from the name of the current directory and the
# source is automatically linked against OpenImageIO.  Additional libraries
# (for example, libpng) may be specified after the optionl LINK_LIBRARIES
# keyword.
#
macro (add_oiio_plugin)
    cmake_parse_arguments (_plugin "" "" "LINK_LIBRARIES" ${ARGN})
    set (_target_name ${CMAKE_CURRENT_SOURCE_DIR})
    # Get the name of the current directory and use it as the target name.
    get_filename_component (_target_name ${CMAKE_CURRENT_SOURCE_DIR} NAME)
    add_library (${_target_name} SHARED ${_plugin_UNPARSED_ARGUMENTS})
    target_link_libraries (${_target_name} ${OPENIMAGEIO_LIBRARIES} ${_plugin_LINK_LIBRARIES})
    set_target_properties (${_target_name} PROPERTIES PREFIX "" FOLDER "Plugins")

    if (VERBOSE)
        message(STATUS "Setting SOVERSION to: ${SOVERSION}")
    endif ()
    set_target_properties( ${_target_name}
                          PROPERTIES
                          VERSION ${OSL_VERSION_MAJOR}.${OSL_VERSION_MINOR}.${OSL_VERSION_PATCH}
                          SOVERSION ${SOVERSION} )

    install_targets (${_target_name})
endmacro ()



add_oiio_plugin (oslinput.cpp
        LINK_LIBRARIES oslexec)
