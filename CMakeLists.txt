cmake_minimum_required(VERSION 3.22 FATAL_ERROR)
project(AOC2021 VERSION 1.0.0)

message(STATUS "Starting AOC 2021 solver")

foreach(PROB RANGE 1 25)
  if (IS_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/p${PROB}")
    message(STATUS "Found problem ${PROB} directory")
    add_subdirectory("p${PROB}")
  endif()
endforeach()

message(STATUS "Completed AOC 2021 solver")
