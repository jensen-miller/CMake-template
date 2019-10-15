# CMakeList.txt : CMake project for TemplatedCMake, include source and define
# project specific logic here.
#
cmake_minimum_required (VERSION 3.8)

# Add source to this project's executable.
add_executable ("my-example" "my_example.cpp" "my_example.hpp")
set_target_properties("my-example" PROPERTIES FOLDER applications)

include_directories("my-example" PUBLIC "../../include")
target_link_libraries("my-example" PUBLIC "${PROJECT_LIB_NAME}")