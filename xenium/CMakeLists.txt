add_library(xenium INTERFACE)

target_sources(xenium INTERFACE $<BUILD_INTERFACE:${XENIUM_FILES}>)

target_include_directories(xenium INTERFACE
                       $<BUILD_INTERFACE:${xenium_SOURCE_DIR}/xenium>>
					   )