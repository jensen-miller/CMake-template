cmake_minimum_required(VERSION 3.11)
project("${PROJECT_NAME}-tests" CXX)
add_definitions(-DUNIT_TESTS)


if ("${CMAKE_SOURCE_DIR}" STREQUAL "${CMAKE_BINARY_DIR}")
	message(FATAL_ERROR "You cannot build in a directory that contains a cmake file.")
endif()


set(CMAKE_CXX_STANDARD 11)	#	c++11
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF) #	not using compiler extensions

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib")
set(CMAKE_BINARY_DIR "${CMAKE_BINARY_DIR}/bin")

#	CMake enable testing
enable_testing()
set(PROJECT_TEST_NAME templated-project-tests-gtestsuite)

include (FetchContent)

#
#	Download Google/googletest
#

#	Git googletest package
FetchContent_Declare(
	googletest
	GIT_REPOSITORY https://github.com/google/googletest.git
	GIT_TAG v1.10.x
)

FetchContent_GetProperties(googletest)
if (NOT googletest_POPULATED)
	FetchContent_Populate(googletest)
	add_subdirectory("${googletest_SOURCE_DIR}" "${googletest_BINARY_DIR}")

	set_target_properties(gmock PROPERTIES FOLDER extern/google)
	set_target_properties(gmock_main PROPERTIES FOLDER extern/google)
	set_target_properties(gtest PROPERTIES FOLDER extern/google)
	set_target_properties(gtest_main PROPERTIES FOLDER extern/google)
endif()