file(GLOB_RECURSE BENCHMARK_FILES "*.cpp" "*.hpp")

add_executable(benchmark ${BENCHMARK_FILES})

include_directories(
	${xenium_SOURCE_DIR}/extern/json/include
	${xenium_SOURCE_DIR}/extern/config/include
)

target_link_libraries(benchmark PRIVATE xenium)

if(CMAKE_THREAD_LIBS_INIT)
	target_link_libraries(benchmark "${CMAKE_THREAD_LIBS_INIT}")
endif()

if(WITH_LIBCDS)
	find_package(LibCDS CONFIG REQUIRED)
	target_link_libraries(benchmark LibCDS::cds)
	target_compile_definitions(benchmark PRIVATE WITH_LIBCDS CDS_THREADING_CXX11)
endif()

if(MSVC)
	target_compile_options(benchmark PRIVATE /bigobj)# /W4 /WX)
else()
	target_compile_options(benchmark PRIVATE -Wall -Wextra -Wno-error=cpp)
endif()
