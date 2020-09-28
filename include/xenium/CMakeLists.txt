file(GLOB_RECURSE XENIUM_FILES *.hpp)

add_library(xenium INTERFACE)

target_sources(xenium INTERFACE $<BUILD_INTERFACE:${XENIUM_FILES}>)

target_include_directories(xenium INTERFACE
                       $<BUILD_INTERFACE:${xenium_SOURCE_DIR}/include>
					   $<INSTALL_INTERFACE:${XENIUM_INC_INSTALL_DIR}>
					   )

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")

include(CMakePackageConfigHelpers)
configure_package_config_file(
	"${xenium_SOURCE_DIR}/cmake/${PROJECT_NAME}Config.cmake.in"
	"${project_config}"
	INSTALL_DESTINATION ${XENIUM_CMAKE_CONFIG_INSTALL_DIR})
write_basic_package_version_file("${version_config}" COMPATIBILITY SameMajorVersion)

if (XENIUM_ENABLE_INSTALL)
	install(TARGETS xenium
			EXPORT ${targets_export_name}
			ARCHIVE DESTINATION ${XENIUM_ARCHIVE_INSTALL_DIR}
			LIBRARY DESTINATION ${XENIUM_LIBRARY_INSTALL_DIR}
			RUNTIME DESTINATION ${XENIUM_RUNTIME_INSTALL_DIR}
			FRAMEWORK DESTINATION ${XENIUM_FRAMEWORK_INSTALL_DIR})

	install(
		DIRECTORY "${xenium_SOURCE_DIR}/include/xenium"
		DESTINATION ${XENIUM_INC_INSTALL_DIR}
		FILES_MATCHING PATTERN "*.hpp")

	install(
      FILES "${project_config}" "${version_config}"
      DESTINATION "${XENIUM_CMAKE_CONFIG_INSTALL_DIR}")

	install(
		EXPORT ${targets_export_name}
		DESTINATION ${XENIUM_CMAKE_CONFIG_INSTALL_DIR})
endif()