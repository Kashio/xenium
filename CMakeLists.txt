cmake_minimum_required(VERSION 3.12.0)

project(xenium
	VERSION 0.0.1
	LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Threads REQUIRED)
find_package(Doxygen)

option(XENIUM_BUILD_DOCUMENTATION "Create the HTML based documentation (requires Doxygen)" ${DOXYGEN_FOUND})
option(XENIUM_ENABLE_TESTING "Enable testing of the xenium library." ON)
option(XENIUM_ENABLE_INSTALL "Enable installation of xenium. (Projects embedding benchmark may want to turn this OFF.)" ON)

list(APPEND CMAKE_MODULE_PATH "${xenium_SOURCE_DIR}/cmake")

if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
	set(XENIUM_INC_INSTALL_DIR "include")
	set(XENIUM_RUNTIME_INSTALL_DIR "bin")
	set(XENIUM_LIBRARY_INSTALL_DIR "lib")
	set(XENIUM_ARCHIVE_INSTALL_DIR "lib")
	set(XENIUM_FRAMEWORK_INSTALL_DIR "lib")
	set(XENIUM_CMAKE_CONFIG_INSTALL_DIR "lib/xenium/cmake")
	set(XENIUM_ADDITIONAL_FILES_INSTALL_DIR "lib/xenium")
	set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Linux" OR ${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
	include(GNUInstallDirs)
	set(XENIUM_INC_INSTALL_DIR "${CMAKE_INSTALL_INCLUDEDIR}")
	set(XENIUM_RUNTIME_INSTALL_DIR "${CMAKE_INSTALL_BINDIR}")
	set(XENIUM_LIBRARY_INSTALL_DIR "${CMAKE_INSTALL_LIBDIR}")
	set(XENIUM_ARCHIVE_INSTALL_DIR "${CMAKE_INSTALL_LIBDIR}")
	set(XENIUM_FRAMEWORK_INSTALL_DIR "${CMAKE_INSTALL_LIBDIR}")
	set(XENIUM_CMAKE_CONFIG_INSTALL_DIR "${CMAKE_INSTALL_LIBDIR}/xenium/cmake")
	set(XENIUM_ADDITIONAL_FILES_INSTALL_DIR "${CMAKE_INSTALL_DATADIR}/xenium")
else()
	message(FATAL_ERROR "Could not set install folders for this platform!")
endif()

include(FetchContent)
if(${CMAKE_VERSION} VERSION_LESS 3.14)
	include(add_FetchContent_MakeAvailable)
endif()

if(CMAKE_BUILD_TYPE MATCHES Debug)
	add_definitions(-DDEBUG)
elseif(CMAKE_BUILD_TYPE MATCHES RelWithDebug)
	add_definitions(-DNDEBUG)
elseif(CMAKE_BUILD_TYPE MATCHES Release)
	add_definitions(-DNDEBUG)
endif()

add_subdirectory("include/xenium")

if(XENIUM_BUILD_DOCUMENTATION)
	if(NOT DOXYGEN_FOUND)
		message(FATAL_ERROR "Doxygen is needed to build the documentation.")
	endif()

	add_custom_target(doc
			COMMAND ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/doc/Doxyfile
			WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/doc
			COMMENT "Generating API documentation with Doxygen"
			VERBATIM)
endif()

if((CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME) AND XENIUM_ENABLE_TESTING)
	enable_testing()
	add_subdirectory(test)
	add_subdirectory(benchmarks)
endif()
