# Problem 5: https://adventofcode.com/2021/day/5

set(INPUT_FILE "${CMAKE_CURRENT_SOURCE_DIR}/input")

# Part 1 - Find overlaps for horizontal/vertical only
file(STRINGS "${INPUT_FILE}" LINES)

set(POINT_COUNT 0)
set(MAX_X 0)
set(MAX_Y 0)

# Get points (only ones in horiz/vert lines)
foreach (LINE IN LISTS LINES)
  string(REPLACE "," ";" LINE "${LINE}")
  string(REPLACE " -> " ";" LINE "${LINE}")

  list(GET LINE 0 X0)
  list(GET LINE 1 Y0)
  list(GET LINE 2 X1)
  list(GET LINE 3 Y1)

  if (X0 GREATER MAX_X)
    set(MAX_X ${X0})
  endif()

  if (X1 GREATER MAX_X)
    set(MAX_X ${X1})
  endif()

  if (Y0 GREATER MAX_Y)
    set(MAX_Y ${Y0})
  endif()

  if (Y1 GREATER MAX_Y)
    set(MAX_Y ${Y1})
  endif()

  if (X0 EQUAL X1 OR Y0 EQUAL Y1)
    set("POINT${POINT_COUNT}" "${LINE}")
    list(APPEND POINTS "POINT${POINT_COUNT}")
    math(EXPR POINT_COUNT "${POINT_COUNT} + 1")
  endif()
endforeach()

foreach(Y RANGE 0 ${MAX_Y})
  foreach(X RANGE 0 ${MAX_X})
    list(APPEND "ROW${Y}" ".")
  endforeach()
endforeach()

macro(printGrid)
  foreach(Y RANGE 0 ${MAX_Y})
    message("${ROW${Y}}")
  endforeach()
  message("----------------------------------------------")
endmacro()

foreach (POINT_PAIR IN LISTS POINTS)
  list(GET "${POINT_PAIR}" 0 X0)
  list(GET "${POINT_PAIR}" 1 Y0)
  list(GET "${POINT_PAIR}" 2 X1)
  list(GET "${POINT_PAIR}" 3 Y1)

  if (X0 EQUAL X1)
    foreach (Y RANGE ${Y0} ${Y1})
      list(TRANSFORM "ROW${Y}" APPEND "A" AT ${X0})
    endforeach()
  else() # Y0 == Y1
    if (X0 LESS_EQUAL X1)
      list(TRANSFORM "ROW${Y0}" APPEND "A" FOR ${X0} ${X1})
    else()
      list(TRANSFORM "ROW${Y0}" APPEND "A" FOR ${X1} ${X0})
    endif()
  endif()
endforeach()

set(SUM 0)

foreach (Y RANGE 0 ${MAX_Y})
  foreach (X RANGE 0 ${MAX_X})
    list(GET "ROW${Y}" ${X} COUNTER)
    string(LENGTH "${COUNTER}" LEN)
    if(LEN GREATER 2)
      math(EXPR SUM "${SUM} + 1")
    endif()
  endforeach()
endforeach()

message(STATUS "\tPart 1: ${SUM}")

# Part 2

set(POINTS "")
set(POINT_COUNT 0)

foreach (LINE IN LISTS LINES)
  string(REPLACE "," ";" LINE "${LINE}")
  string(REPLACE " -> " ";" LINE "${LINE}")

  list(GET LINE 0 X0)
  list(GET LINE 1 Y0)
  list(GET LINE 2 X1)
  list(GET LINE 3 Y1)

  if (NOT ${X0} EQUAL ${X1} AND NOT ${Y0} EQUAL ${Y1})
    set("POINT${POINT_COUNT}" "${LINE}")
    list(APPEND POINTS "POINT${POINT_COUNT}")
    math(EXPR POINT_COUNT "${POINT_COUNT} + 1")
  endif()
endforeach()

foreach (POINT_PAIR IN LISTS POINTS)
  list(GET "${POINT_PAIR}" 0 X0)
  list(GET "${POINT_PAIR}" 1 Y0)
  list(GET "${POINT_PAIR}" 2 X1)
  list(GET "${POINT_PAIR}" 3 Y1)

  if (X0 LESS X1)
    set(STEPX 1)
  else()
    set(STEPX -1)
  endif()

  if (Y0 LESS Y1)
    set(STEPY 1)
  else()
    set(STEPY -1)
  endif()

  foreach (Y RANGE ${Y0} ${Y1} ${STEPY})
    list(TRANSFORM "ROW${Y}" APPEND "A" AT ${X0})
    math(EXPR X0 "${X0} + ${STEPX}")
  endforeach()
endforeach()

set(SUM 0)

foreach (Y RANGE 0 ${MAX_Y})
  foreach (X RANGE 0 ${MAX_X})
    list(GET "ROW${Y}" ${X} COUNTER)
    string(LENGTH "${COUNTER}" LEN)
    if(LEN GREATER 2)
      math(EXPR SUM "${SUM} + 1")
    endif()
  endforeach()
endforeach()

message(STATUS "\tPart 2: ${SUM}")
