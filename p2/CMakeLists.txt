# Problem 2: https://adventofcode.com/2021/day/2

# Part 1
file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/input" LINES)

set(DEPTH 0)
set(HORIZ 0)

foreach (LINE IN LISTS LINES)
  string(REPLACE " " ";" CMD_LIST "${LINE}")
  list(GET CMD_LIST 0 MOVE_TYPE)
  list(GET CMD_LIST 1 MOVE_SIZE)

  if (MOVE_TYPE STREQUAL "forward")
    math(EXPR HORIZ "${HORIZ} + ${MOVE_SIZE}")
  elseif (MOVE_TYPE STREQUAL "down")
    math(EXPR DEPTH "${DEPTH} + ${MOVE_SIZE}")
  else() # up
    math(EXPR DEPTH "${DEPTH} - ${MOVE_SIZE}")
  endif()
endforeach()

math(EXPR FINAL_LOC "${DEPTH} * ${HORIZ}")
message(STATUS "\tPart 1: ${FINAL_LOC}")

# Part 2
set(DEPTH 0)
set(HORIZ 0)
set(AIM 0)

foreach (LINE IN LISTS LINES)
  string(REPLACE " " ";" CMD_LIST "${LINE}")
  list(GET CMD_LIST 0 MOVE_TYPE)
  list(GET CMD_LIST 1 MOVE_SIZE)

  if (MOVE_TYPE STREQUAL "forward")
    math(EXPR HORIZ "${HORIZ} + ${MOVE_SIZE}")
    math(EXPR DEPTH "${DEPTH} + ${AIM} * ${MOVE_SIZE}")
  elseif (MOVE_TYPE STREQUAL "down")
    math(EXPR AIM "${AIM} + ${MOVE_SIZE}")
  else() # up
    math(EXPR AIM "${AIM} - ${MOVE_SIZE}")
  endif()
endforeach(LINE IN LISTS LINES)

math(EXPR FINAL_LOC "${DEPTH} * ${HORIZ}")
message(STATUS "\tPart 1: ${FINAL_LOC}")
