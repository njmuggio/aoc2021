# Problem 6: https://adventofcode.com/2021/day/6

set(INPUT_FILE "${CMAKE_CURRENT_SOURCE_DIR}/input")

# Part 1 - Find ages after 80 days
file(STRINGS "${INPUT_FILE}" AGES)
string(REPLACE "," ";" AGES "${AGES}")

set(REMAIN0 0)
set(REMAIN1 0)
set(REMAIN2 0)
set(REMAIN3 0)
set(REMAIN4 0)
set(REMAIN5 0)
set(REMAIN6 0)
set(REMAIN7 0)
set(REMAIN8 0)

# Count number in each bin
foreach (AGE IN LISTS AGES)
  math(EXPR "REMAIN${AGE}" "${REMAIN${AGE}} + 1")
endforeach()

foreach (DAY RANGE 1 80)
  set(NEW_SPAWN ${REMAIN0})
  set(REMAIN0 ${REMAIN1})
  set(REMAIN1 ${REMAIN2})
  set(REMAIN2 ${REMAIN3})
  set(REMAIN3 ${REMAIN4})
  set(REMAIN4 ${REMAIN5})
  set(REMAIN5 ${REMAIN6})
  math(EXPR REMAIN6 "${REMAIN7} + ${NEW_SPAWN}")
  set(REMAIN7 ${REMAIN8})
  set(REMAIN8 ${NEW_SPAWN})
endforeach()

math(EXPR PART1_ANSWER "${REMAIN0} + ${REMAIN1} + ${REMAIN2} + ${REMAIN3} + ${REMAIN4} + ${REMAIN5} + ${REMAIN6} + ${REMAIN7} + ${REMAIN8}")

message(STATUS "\tPart 1: ${PART1_ANSWER}")

# Part 2

foreach (DAY RANGE 81 256)
  set(NEW_SPAWN ${REMAIN0})
  set(REMAIN0 ${REMAIN1})
  set(REMAIN1 ${REMAIN2})
  set(REMAIN2 ${REMAIN3})
  set(REMAIN3 ${REMAIN4})
  set(REMAIN4 ${REMAIN5})
  set(REMAIN5 ${REMAIN6})
  math(EXPR REMAIN6 "${REMAIN7} + ${NEW_SPAWN}")
  set(REMAIN7 ${REMAIN8})
  set(REMAIN8 ${NEW_SPAWN})
endforeach()

math(EXPR PART2_ANSWER "${REMAIN0} + ${REMAIN1} + ${REMAIN2} + ${REMAIN3} + ${REMAIN4} + ${REMAIN5} + ${REMAIN6} + ${REMAIN7} + ${REMAIN8}")

message(STATUS "\tPart 2: ${PART2_ANSWER}")
