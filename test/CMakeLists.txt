file(GLOB_RECURSE TEST_FILES *.cpp)

add_executable(testlib ${TEST_FILES})

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

target_link_libraries(testlib PRIVATE xenium gtest_main)

if(CMAKE_THREAD_LIBS_INIT)
	target_link_libraries(testlib "${CMAKE_THREAD_LIBS_INIT}")
endif()

if(MSVC)
	target_compile_options(testlib PRIVATE /bigobj /W4)# /WX)
else()
	target_compile_options(testlib PRIVATE -Wall -Wextra -Werror -Wno-error=cpp)
endif()

add_test(testlib testlib)