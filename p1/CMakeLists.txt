# Problem 1: https://adventofcode.com/2021/day/1

# Part 1
file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/input" NUMS)

# First measurement is our starting point
list(POP_FRONT NUMS MEAS)
set(INC_COUNT 0)

# Count every time NUMS[i] > NUMS[i-1]
foreach (NUM IN LISTS NUMS)
  if (NUM GREATER MEAS)
    math(EXPR INC_COUNT "${INC_COUNT} + 1")
  endif()

  set(MEAS ${NUM})
endforeach()

message(STATUS "\tPart 1: ${INC_COUNT}")

# Part 2
file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/input" NUMS)

# Initialize window
list(POP_FRONT NUMS WIN1)
list(POP_FRONT NUMS WIN2)
list(POP_FRONT NUMS WIN3)

set(INC_COUNT 0)

foreach (NUM IN LISTS NUMS)
  math(EXPR FIRST "${WIN1} + ${WIN2} + ${WIN3}")
  math(EXPR SECOND "${WIN2} + ${WIN3} + ${NUM}")

  if (SECOND GREATER FIRST)
    math(EXPR INC_COUNT "${INC_COUNT} + 1")
  endif()

  set(WIN1 ${WIN2})
  set(WIN2 ${WIN3})
  set(WIN3 ${NUM})
endforeach()

message(STATUS "\tPart 2: ${INC_COUNT}")
