cmake_minimum_required(VERSION 3.21)

project(test_project)

set(CMAKE_CXX_STANDARD 17)

add_executable(testapp)
target_sources(testapp PRIVATE main.cpp)
file(GLOB SOURCES_FROM_DIR_1 "${CMAKE_CURRENT_SOURCE_DIR}/dir_1/*.cpp")
target_sources(testapp PRIVATE ${SOURCES_FROM_DIR_1})


message(WARNING "Root CMAKE_CURRENT_LIST_DIR is  ${CMAKE_CURRENT_LIST_DIR}")
message(WARNING "Root CMAKE_CURRENT_SOURCE_DIR   is  ${CMAKE_CURRENT_SOURCE_DIR}")

# Example to demostrate CMAKE_CURRENT_LIST_DIR and CMAKE_CURRENT_SOURCE_DIR
# when use include and add_subdirectory
include(${CMAKE_CURRENT_LIST_DIR}/liba/CMakeLists.txt)
add_subdirectory(${CMAKE_CURRENT_LIST_DIR}/libb)

target_link_libraries(testapp liba libb)
target_include_directories(testapp PRIVATE ${CMAKE_CURRENT_LIST_DIR}/liba)
target_include_directories(testapp PUBLIC ${CMAKE_CURRENT_LIST_DIR}/libb)
target_include_directories(testapp PUBLIC ${CMAKE_CURRENT_LIST_DIR}/dir_1)