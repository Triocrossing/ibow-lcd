cmake_minimum_required(VERSION 3.5)
project(ibow-lcd)

if(UNIX AND NOT APPLE)
 set(LINUX TRUE)
endif()
message(STATUS ">>> Main CMakeLists")
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

if(CMAKE_BUILD_TYPE MATCHES Release)
  message(STATUS "Setting Release options")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -O3 -march=native")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -O3 -march=native")
elseif(CMAKE_BUILD_TYPE MATCHES Debug)
  message(STATUS "Setting Debug options")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -O1 -pg -march=native")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -O1 -pg -march=native")
endif()

set(CMAKE_CXX_FLAGS "-lboost_filesystem")

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

find_package(OpenCV REQUIRED) # openCV

find_package(OpenMP REQUIRED) # OpenMP
if (OPENMP_FOUND)
  message(STATUS "OpenMP found")
  set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
  set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
elseif()
  message(STATUS "OpenMP NOT found")

endif()


# headers
include_directories(
${PROJECT_SOURCE_DIR}/include
# ${PROJECT_SOURCE_DIR}/evaluation
${PROJECT_SOURCE_DIR}/external # including third party
)

# Copy test images to the binary directory
file(GLOB JSON "evaluation/*.json")
file(COPY ${JSON}
     DESTINATION ${CMAKE_CURRENT_BINARY_DIR})

add_subdirectory(${PROJECT_SOURCE_DIR}/external/obindex2) 

# cpps
set(SOURCE_FILES  ${PROJECT_SOURCE_DIR}/src/lcdetector.cc
                  # ${PROJECT_SOURCE_DIR}/src/.cc
                  ${PROJECT_SOURCE_DIR}/src/main.cc
                  # ${PROJECT_SOURCE_DIR}/external/obindex2/lib/src/binary_index.cc
)


set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

# executable
add_executable(demo
               ${SOURCE_FILES}
               src/main.cc)
target_link_libraries(demo
${OpenCV_LIBS}
)
target_link_libraries(demo obindex2)

target_compile_features(demo PRIVATE cxx_std_17)