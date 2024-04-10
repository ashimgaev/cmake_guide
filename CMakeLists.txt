cmake_minimum_required(VERSION 3.21)

project(test_project)

set(CMAKE_CXX_STANDARD 17)

# Section: variables example
set(MY_VAR_LIST "one" "two")
message(WARNING "[Root] MY_VAR_LIST:  ${MY_VAR_LIST}")
set(MY_VAR_LIST "three;for")
message(WARNING "[Root] MY_VAR_LIST:  ${MY_VAR_LIST}")
set(MY_VAR_LIST ${MY_VAR_LIST} "five" "six")
message(WARNING "[Root] MY_VAR_LIST:  ${MY_VAR_LIST}")
# The MY_OPTION is intendend to be set from cmake command line like cmake -DMY_OPTION=ON
option(MY_OPTION "This is settable from the command line" OFF)
message(WARNING "[Root] MY_OPTION:  ${MY_OPTION}")

# Define out target
add_executable(testapp)

# Section: how to add source files to target
# a. One by one
target_sources(testapp PRIVATE main.cpp)
# b. Using glob to filter
file(GLOB SOURCES_FROM_DIR_1 "${CMAKE_CURRENT_SOURCE_DIR}/dir_1/*.cpp")
target_sources(testapp PRIVATE ${SOURCES_FROM_DIR_1})

# Example to demostrate CMAKE_CURRENT_LIST_DIR and CMAKE_CURRENT_SOURCE_DIR
# when use include and add_subdirectory
set(GLOBAL_VAR_TEST "MyGlabalVar")
message(WARNING "[Root] GLOBAL_VAR_TEST is: ${GLOBAL_VAR_TEST}")
message(WARNING "[Root] CMAKE_CURRENT_LIST_DIR is:  ${CMAKE_CURRENT_LIST_DIR}")
message(WARNING "[Root] CMAKE_CURRENT_SOURCE_DIR is:  ${CMAKE_CURRENT_SOURCE_DIR}")
# Add liba using include function. The CMAKE_CURRENT_SOURCE_DIR will not be changed. The CMAKE_CURRENT_LIST_DIR will direct to liba
include(${CMAKE_CURRENT_LIST_DIR}/liba/CMakeLists.txt)

# GLOBAL_VAR_TEST is visible during include and can be changed there
message(WARNING "[Root] GLOBAL_VAR_TEST check: ${GLOBAL_VAR_TEST}")

# LIBA_TEST_VAR should be valid
message(WARNING "[Root] LIBA_TEST_VAR check: ${LIBA_TEST_VAR}")

# Add liba using add_subdirectory. The CMAKE_CURRENT_SOURCE_DIR will be changed during add_subdirectory stuff. The CMAKE_CURRENT_LIST_DIR will direct to libb
set(GLOBAL_VAR_TEST "MyGlabalVar")
add_subdirectory(${CMAKE_CURRENT_LIST_DIR}/libb)

# GLOBAL_VAR_TEST is visible during add_subdirectory but can't be changed there
message(WARNING "[Root] GLOBAL_VAR_TEST check: ${GLOBAL_VAR_TEST}")

# LIBB_TEST_VAR should be undefined since add_subdirectory creates new scope
message(WARNING "[Root] LIBB_TEST_VAR check: ${LIBB_TEST_VAR}")

target_link_libraries(testapp liba libb)
target_include_directories(testapp PRIVATE ${CMAKE_CURRENT_LIST_DIR}/liba)
target_include_directories(testapp PUBLIC ${CMAKE_CURRENT_LIST_DIR}/libb)
target_include_directories(testapp PUBLIC ${CMAKE_CURRENT_LIST_DIR}/dir_1)

# Section: condition example
set(TEST_VAR "ON")

if(MY_OPTION AND TEST_VAR AND TEST_VAR STREQUAL "ON")
    # If variable is `ON`, `YES`, `TRUE`, `Y`, or non zero number
    message(WARNING "[Root] MY_OPTION:  Enabled!")
else()
    # Note that undefined variables would be `""` thus false
    message(WARNING "[Root] MY_OPTION:  Disabled!")
endif()