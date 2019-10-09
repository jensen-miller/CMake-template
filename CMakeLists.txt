cmake_minimum_required (VERSION 3.8)

set(PROJECT_NAME "templated-project")
set(PROJECT_LIB_NAME "project-library-name")


#
#	Project Name
#
project ("${PROJECT_NAME}")



#
#	Build options
#
option(BUILD_LIBRARY_SHARED "Build the library as a shared object, will build it as a static library otherwise." OFF)
option(BUILD_EXAMPLES "Build the samples." ON)
option(BUILD_TESTS "Build the tests." OFF)



#
#	Include sub-projects.
#

#	Examples
if (BUILD_EXAMPLES)
	add_subdirectory ("${PROJECT_SOURCE_DIR}/examples/my-example")
endif()



#
#	Discover all library code
#
file(GLOB_RECURSE SOURCES FOLLOW_SYMLINKS "${PROJECT_SOURCE_DIR}/src/*.cpp")
file(GLOB_RECURSE INCLUDES FOLLOW_SYMLINKS "${PROJECT_SOURCE_DIR}/include/*.hpp")


#
#	Setup library by root
#
if (BUILD_LIBRARY_SHARED)
	add_library("${PROJECT_LIB_NAME}" STATIC "")
	set_target_properties(${SDK_TARGET_NAME} PROPERTIES SUFFIX ".so")
else()
	add_library("${PROJECT_LIB_NAME}" STATIC "")
	set_target_properties("${PROJECT_LIB_NAME}" PROPERTIES POSITION_INDEPENDENT_CODE ON)
endif()



target_include_directories("${PROJECT_LIB_NAME}" PRIVATE  "${PROJECT_SOURCE_DIR}/include")

target_sources("${PROJECT_LIB_NAME}" PUBLIC "${INCLUDES}")	# include library headers
target_sources("${PROJECT_LIB_NAME}" PRIVATE "${SOURCES}")	# include library source code