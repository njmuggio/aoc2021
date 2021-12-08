# Problem 7: https://adventofcode.com/2021/day/7

set(INPUT_FILE "${CMAKE_CURRENT_SOURCE_DIR}/input")

# Part 1
file(STRINGS "${INPUT_FILE}" POSITIONS)
string(REPLACE "," ";" POSITIONS "${POSITIONS}")

# Find the median
list(SORT POSITIONS COMPARE NATURAL)
list(LENGTH POSITIONS LEN)
math(EXPR LEN "${LEN} / 2")
list(GET POSITIONS ${LEN} MEDIAN)

set(TOTAL_DIST 0)

foreach (POS IN LISTS POSITIONS)
  math(EXPR DIST "${POS} - ${MEDIAN}")
  if (DIST GREATER_EQUAL 0)
    math(EXPR TOTAL_DIST "${TOTAL_DIST} + ${DIST}")
  else()
    math(EXPR TOTAL_DIST "${TOTAL_DIST} - ${DIST}")
  endif()
endforeach()

message(STATUS "\tPart 1: ${TOTAL_DIST}")

# Part 2

# Find the average
set(TOTAL 0)
foreach (POS IN LISTS POSITIONS)
  math(EXPR TOTAL "${TOTAL} + ${POS}")
endforeach()

list(LENGTH POSITIONS LEN)
math(EXPR AVERAGE "${TOTAL} / ${LEN}")

# We only have integer division, so that may have rounded down improperly.
# We'll test for both values.

set(TOTAL_DIST_0 0)
foreach (POS IN LISTS POSITIONS)
  math(EXPR N "${POS} - ${AVERAGE}")
  if (N LESS 0)
    string(SUBSTRING "${N}" 1 -1 N)
  endif()

  math(EXPR DIST "${N} * (${N} + 1) / 2")
  # message("N: abs(${POS} - ${AVERAGE})   ${N} * (${N} + 1) / 2 == ${DIST}")

  if (DIST GREATER_EQUAL 0)
    math(EXPR TOTAL_DIST_0 "${TOTAL_DIST_0} + ${DIST}")
  else()
    math(EXPR TOTAL_DIST_0 "${TOTAL_DIST_0} - ${DIST}")
  endif()
endforeach()

set(TOTAL_DIST_1 0)
math(EXPR AVERAGE "${AVERAGE} + 1")
foreach (POS IN LISTS POSITIONS)
  math(EXPR N "${POS} - ${AVERAGE}")
  if (N LESS 0)
    string(SUBSTRING "${N}" 1 -1 N)
  endif()

  math(EXPR DIST "${N} * (${N} + 1) / 2")

  if (DIST GREATER_EQUAL 0)
    math(EXPR TOTAL_DIST_1 "${TOTAL_DIST_1} + ${DIST}")
  else()
    math(EXPR TOTAL_DIST_1 "${TOTAL_DIST_1} - ${DIST}")
  endif()
endforeach()

if (TOTAL_DIST_0 LESS TOTAL_DIST_1)
  set(PART2_ANSWER ${TOTAL_DIST_0})
else()
  set(PART2_ANSWER ${TOTAL_DIST_1})
endif()

message(STATUS "\tPart 2: ${PART2_ANSWER}")
