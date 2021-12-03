# Problem 3: https://adventofcode.com/2021/day/3

# Part 1
file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/input" LINES)
list(GET LINES 0 FIRST_LINE)
string(LENGTH "${FIRST_LINE}" BIT_WIDTH)



macro(countTheBits LIST_OF_LINES)
  set(BIT_COUNTS "")

  foreach(B RANGE 1 ${BIT_WIDTH})
    list(APPEND BIT_COUNTS 0)
  endforeach()

  foreach(LINE IN LISTS ${LIST_OF_LINES})
    # Turn each number into a list
    string(REPLACE "0" "0;" LINE "${LINE}")
    string(REPLACE "1" "1;" LINE "${LINE}")

    foreach(BIT RANGE ${BIT_WIDTH} 1)
      math(EXPR IDX "${BIT_WIDTH} - ${BIT}")
      list(GET LINE ${IDX} TEMP_BIT)

      if (TEMP_BIT STREQUAL "1")
        math(EXPR DEST_IDX "${BIT} - 1")
        list(GET BIT_COUNTS ${DEST_IDX} TEMP_COUNT)
        math(EXPR TEMP_COUNT "${TEMP_COUNT} + 1")
        list(INSERT BIT_COUNTS ${DEST_IDX} "${TEMP_COUNT}")
        list(REMOVE_AT BIT_COUNTS ${BIT})
      endif()
    endforeach()
  endforeach()
endmacro()

countTheBits(LINES)

list(LENGTH LINES TOTAL_NUM_COUNT)
math(EXPR MAJORITY_THRESHOLD "${TOTAL_NUM_COUNT} / 2")
set(MULT 1)

set(GAMMA 0)
set(EPSILON 0)

foreach(COUNT IN LISTS BIT_COUNTS)
  if (COUNT GREATER MAJORITY_THRESHOLD)
    math(EXPR GAMMA "${GAMMA} + ${MULT}")
  else()
    math(EXPR EPSILON "${EPSILON} + ${MULT}")
  endif()

  math(EXPR MULT "${MULT} * 2")
endforeach()

math(EXPR PART1_ANSWER "${GAMMA} * ${EPSILON}")

message(STATUS "\tPart 1: ${PART1_ANSWER}")

# Part 2

foreach(LINE IN LISTS LINES)
  list(APPEND O2_LINES "${LINE}")
  list(APPEND CO2_LINES "${LINE}")
endforeach()

set(O2_FILTER "^")
set(CO2_FILTER "^")

set(COUNT_IDX 0)
math(EXPR COUNT_MAX "${BIT_WIDTH} - 1")

while(COUNT_IDX LESS BIT_WIDTH)
  countTheBits(O2_LINES)
  list(REVERSE BIT_COUNTS)
  list(GET BIT_COUNTS ${COUNT_IDX} COUNT)
  list(LENGTH O2_LINES MAJORITY_THRESHOLD)
  math(EXPR MAJORITY_THRESHOLD "(${MAJORITY_THRESHOLD} + 1) / 2")

  if(COUNT GREATER_EQUAL MAJORITY_THRESHOLD)
    # 1s >= 0s
    string(APPEND O2_FILTER "1")
  else()
    # 1s < 0s
    string(APPEND O2_FILTER "0")
  endif()

  countTheBits(CO2_LINES)
  list(REVERSE BIT_COUNTS)
  list(GET BIT_COUNTS ${COUNT_IDX} COUNT)
  list(LENGTH CO2_LINES MAJORITY_THRESHOLD)
  math(EXPR MAJORITY_THRESHOLD "(${MAJORITY_THRESHOLD} + 1) / 2")

  if(COUNT GREATER_EQUAL MAJORITY_THRESHOLD)
    # 1s >= 0s
    string(APPEND CO2_FILTER "0")
  else()
    # 1s < 0s
    string(APPEND CO2_FILTER "1")
  endif()

  list(FILTER O2_LINES INCLUDE REGEX "${O2_FILTER}.*")
  list(FILTER CO2_LINES INCLUDE REGEX "${CO2_FILTER}.*")

  list(LENGTH O2_LINES O2_LENGTH)
  list(LENGTH CO2_LINES CO2_LENGTH)

  if (O2_LENGTH EQUAL 1)
    list(GET O2_LINES 0 O2_VAL)
  endif()

  if (CO2_LENGTH EQUAL 1)
    list(GET CO2_LINES 0 CO2_VAL)
  endif()

  math(EXPR COUNT_IDX "${COUNT_IDX} + 1")
endwhile()

set(MULT 1)

set(O2_FINAL 0)
set(CO2_FINAL 0)

foreach(BIT RANGE ${BIT_WIDTH} 1)
  math(EXPR IDX "${BIT} - 1")
  string(SUBSTRING "${O2_VAL}" ${IDX} 1 O2_BIT)
  string(SUBSTRING "${CO2_VAL}" ${IDX} 1 CO2_BIT)

  if (O2_BIT STREQUAL "1")
    math(EXPR O2_FINAL "${O2_FINAL} + ${MULT}")
  endif()

  if (CO2_BIT STREQUAL "1")
    math(EXPR CO2_FINAL "${CO2_FINAL} + ${MULT}")
  endif()

  math(EXPR MULT "${MULT} * 2")
endforeach()

math(EXPR PART2_ANSWER "${O2_FINAL} * ${CO2_FINAL}")

message(STATUS "\tPart 2: ${PART2_ANSWER}")
