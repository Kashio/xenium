project(xenium)
cmake_minimum_required(VERSION 3.0)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Threads REQUIRED)
find_package(Doxygen)

option(XENIUM_BUILD_DOCUMENTATION "Create the HTML based documentation (requires Doxygen)" ${DOXYGEN_FOUND})
option(XENIUM_ENABLE_TESTING "Enable testing of the xenium library." ON)

list(APPEND CMAKE_MODULE_PATH "${xenium_SOURCE_DIR}/cmake")

file(GLOB_RECURSE XENIUM_FILES xenium/*.hpp)

file(GLOB_RECURSE TEST_FILES test/*.cpp)

file(GLOB_RECURSE BENCHMARK_FILES benchmarks/*.cpp)

include_directories(.)

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
endif()
