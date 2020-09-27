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

add_test(testlib testlib)