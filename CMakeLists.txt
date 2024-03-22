#Request the minimum version of CMake, in case of lower version throws error.
#See: https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html
cmake_minimum_required(VERSION 3.25 FATAL_ERROR)

#Define your projet
#See: https://cmake.org/cmake/help/latest/command/project.html
project(
    "FirstProjectInCMake"
    VERSION 1.2.0
    DESCRIPTION "CMake C Project Template for Operating System II"
    LANGUAGES C
)

#Enable testing and add the tests
include(CTest)

#Define the C standard, we are going to use std17
#See: https://cmake.org/cmake/latest/variable/CMAKE_CXX_STANDARD.html
set(CMAKE_C_STANDARD 17)

#Include the 'include' directory, where the headers are located
#See: https://cmake.org/cmake/help/latest/command/include_directories.html
include_directories(include)

#To automatic download external deps, we need use 'FetchContent'
#See:
include(FetchContent)

#In this example we are going to use Unity to download and to test our program
set(UNITY_GIT_URL "https://github.com/ThrowTheSwitch/Unity.git")

#Enable debug
set(FETCHCONTENT_QUIET OFF)

FetchContent_Declare(
    Unity
    GIT_REPOSITORY ${UNITY_GIT_URL}
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/external/Unity
    UPDATE_COMMAND "" #Disable the update command
)

#Insures the named dependencies have been populated, either by an earlier call or by populating them its
FetchContent_MakeAvailable(Unity)

#Add the 'src' directory, where the source files are located
#See: https://cmake.org/cmake/help/latest/command/file.html#glob
file(GLOB_RECURSE SOURCES "src/*.c")

#Create the executable
#See: https://cmake.org/cmake/help/latest/command/add_executable.html
add_executable(${PROJECT_NAME} ${SOURCES})

#Compile the libraries
#See: https://cmake.org/cmake/help/latest/command/add_subdirectory.html
add_subdirectory(lib/libdyn)
add_subdirectory(lib/libsta)

#Include headers in Lib
#See: https://cmake.org/cmake/help/latest/command/target_include_directories.html
target_include_directories(${PROJECT_NAME} PUBLIC lib/libdyn/include)
target_include_directories(${PROJECT_NAME} PUBLIC lib/libsta/include)

#Link the libraries
#See: https://cmake.org/cmake/help/latest/command/target_link_libraries.html
target_link_libraries(${PROJECT_NAME} StaticCLib DynamicCLib)

#add subdirectory of test
if(RUN_TESTS EQUAL 1 OR RUN_COVERAGE EQUAL 1)
    #See:
    add_subdirectory(tests)
endif()
