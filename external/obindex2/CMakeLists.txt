cmake_minimum_required(VERSION 3.5)
project(obindex2)

if(UNIX AND NOT APPLE)
 set(LINUX TRUE)
endif()
message(STATUS ">>> obindex CMakeLists")
# Recognising different systems
if(LINUX) 
  message(STATUS ">>> Linux Os")
  add_compile_definitions(USE_LINUX=true)
# linux stuff here
elseif(APPLE)
 message(STATUS ">>> Mac OS")
 add_compile_definitions(USE_MAC=true)
# stuff that should happen not on Linux 
endif()

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# opencv is required 
find_package(OpenCV REQUIRED)
set(CMAKE_CXX_FLAGS "-lboost_filesystem")

# headers
include_directories(
${PROJECT_SOURCE_DIR}/lib
)

# cpps
set(SOURCE_FILES  ${PROJECT_SOURCE_DIR}/lib/src/binary_descriptor.cc
                  ${PROJECT_SOURCE_DIR}/lib/src/binary_index.cc
                  ${PROJECT_SOURCE_DIR}/lib/src/binary_tree_node.cc
                  ${PROJECT_SOURCE_DIR}/lib/src/binary_tree.cc
                  # tests
                  # ${PROJECT_SOURCE_DIR}/lib/tests/test_search.cc
                  ${PROJECT_SOURCE_DIR}/lib/tests/test_bindex.cc
)
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
add_library(obindex2 ${SOURCE_FILES})
# add_executable(obindex2 ${SOURCE_FILES})
target_compile_features(obindex2 PRIVATE cxx_std_17)
# set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
target_link_libraries(obindex2 ${OpenCV_LIBS})