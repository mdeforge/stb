#
# Preamble
#

cmake_minimum_required(VERSION 3.15)
project(stb VERSION 1.0.0 LANGUAGES C)
include(CTest)

#
# Create Target
#

# Add the library
add_library(stb STATIC)
add_library(stb::stb ALIAS stb)

# Add sources to target
target_sources(stb
    PRIVATE
        include/stb/stb_connected_components.h
        include/stb/stb_c_lexer.h
        include/stb/stb_divide.h
        include/stb/stb_ds.h
        include/stb/stb_dxt.h
        include/stb/stb_easy_font.h
        include/stb/stb_herringbone_wang_tile.h
        include/stb/stb_hexwave.h
        include/stb/stb_image.h
        include/stb/stb_image_resize.h
        include/stb/stb_image_write.h
        include/stb/stb_include.h
        include/stb/stb_leakcheck.h
        include/stb/stb_rect_pack.h
        include/stb/stb_sprintf.h
        include/stb/stb_textedit.h
        include/stb/stb_tilemap_editor.h
        include/stb/stb_truetype.h
        include/stb/stb_voxel_render.h
        src/stb_vorbis.c
)

# Postfix debug files
set_target_properties(stb PROPERTIES DEBUG_POSTFIX "d")

#
# Usage Requirements
#

# Add compiler features and definitions
target_compile_features(stb PRIVATE c_std_11)

# Specify include directories to use when compiling a given target
target_include_directories(stb
	PUBLIC
		$<INSTALL_INTERFACE:include/stb>
		$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
	PRIVATE
		${CMAKE_CURRENT_SOURCE_DIR}/include/stb
)

#
# Install and Export
#

# Set the install destination for cmake files
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/stb)

# Install include directory
include(GNUInstallDirs)
install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

# Specify the name of the export set the targets belong to
install(TARGETS stb
	EXPORT stbTargets
	ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}/$<CONFIG>
	LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}/$<CONFIG>
	RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}/$<CONFIG>
	INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

# Export the targets to a script
install(EXPORT stbTargets
	FILE stbTargets.cmake
	NAMESPACE stb::
	DESTINATION ${INSTALL_CONFIGDIR}
)

# Generate a config file that includes the exports
include(CMakePackageConfigHelpers)
configure_package_config_file(
	${CMAKE_CURRENT_LIST_DIR}/cmake/stbConfig.cmake.in
	${CMAKE_CURRENT_BINARY_DIR}/cmake/stbConfig.cmake
	INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
	NO_SET_AND_CHECK_MACRO
	NO_CHECK_REQUIRED_COMPONENTS_MACRO
)

# Generate the version file for the config file
write_basic_package_version_file(
	${CMAKE_CURRENT_BINARY_DIR}/cmake/stbConfigVersion.cmake
	VERSION ${PROJECT_VERSION}
	COMPATIBILITY SameMajorVersion
)

# Install the Config, ConfigVersion and custom Find Module
install(FILES
	#${CMAKE_CURRENT_LIST_DIR}/Findstb.cmake
	${CMAKE_CURRENT_BINARY_DIR}/cmake/stbConfig.cmake
	${CMAKE_CURRENT_BINARY_DIR}/cmake/stbConfigVersion.cmake
	DESTINATION ${INSTALL_CONFIGDIR}
)

# Make this project available from the build directory
export(EXPORT stbTargets
	FILE ${CMAKE_CURRENT_BINARY_DIR}/stbTargets.cmake
	NAMESPACE stb::
)

#
# Testing
#

# TODO