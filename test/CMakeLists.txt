add_executable(testlib ${TEST_FILES} ${XENIUM_FILES})
add_executable(benchmark ${BENCHMARK_FILES} ${XENIUM_FILES})

set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

include_directories(
	${xenium_SOURCE_DIR}/extern/json/include
	${xenium_SOURCE_DIR}/extern/config/include
)

FetchContent_Declare(googletest
                     GIT_REPOSITORY https://github.com/google/googletest
					 GIT_TAG master
        )
FetchContent_MakeAvailable(googletest)

target_link_libraries(testlib INTERFACE xenium PRIVATE gtest_main)

if(CMAKE_THREAD_LIBS_INIT)
	target_link_libraries(testlib "${CMAKE_THREAD_LIBS_INIT}")
	target_link_libraries(benchmark "${CMAKE_THREAD_LIBS_INIT}")
endif()

if(WITH_LIBCDS)
	find_package(LibCDS CONFIG REQUIRED)
	target_link_libraries(benchmark LibCDS::cds)
	target_compile_definitions(benchmark PRIVATE WITH_LIBCDS CDS_THREADING_CXX11)
endif()

if(MSVC)
	target_compile_options(testlib PRIVATE /bigobj /W4)# /WX)
	target_compile_options(benchmark PRIVATE /bigobj)# /W4 /WX)
else()
	target_compile_options(testlib PRIVATE -Wall -Wextra -Werror -Wno-error=cpp)
	target_compile_options(benchmark PRIVATE -Wall -Wextra -Wno-error=cpp)
endif()

add_test(testlib testlib)