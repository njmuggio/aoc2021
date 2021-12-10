# Problem 10: https://adventofcode.com/2021/day/10

set(INPUT_FILE "${CMAKE_CURRENT_SOURCE_DIR}/input")

# Part 1
file(STRINGS "${INPUT_FILE}" LINES)

string(REPLACE "(" "p" LINES "${LINES}")
string(REPLACE ")" "P" LINES "${LINES}")
string(REPLACE "[" "s" LINES "${LINES}")
string(REPLACE "]" "S" LINES "${LINES}")
string(REPLACE "{" "b" LINES "${LINES}")
string(REPLACE "}" "B" LINES "${LINES}")
string(REPLACE "<" "a" LINES "${LINES}")
string(REPLACE ">" "A" LINES "${LINES}")

function(scoreCorruptLine LINE OUT_SCORE)
  string(LENGTH "${LINE}" LEN)
  math(EXPR LEN "${LEN} - 1")

  set("${OUT_SCORE}" 0 PARENT_SCOPE)

  foreach (I RANGE 0 ${LEN})
    string(SUBSTRING "${LINE}" ${I} 1 CHAR)
    if (CHAR STREQUAL "p")
      list(APPEND CHUNK_TERM_STACK "P")
    elseif (CHAR STREQUAL "s")
      list(APPEND CHUNK_TERM_STACK "S")
    elseif (CHAR STREQUAL "b")
      list(APPEND CHUNK_TERM_STACK "B")
    elseif (CHAR STREQUAL "a")
      list(APPEND CHUNK_TERM_STACK "A")
    else()
      list(POP_BACK CHUNK_TERM_STACK EXPECTED)
      if (NOT CHAR STREQUAL "${EXPECTED}")
        if (CHAR STREQUAL "P")
          set("${OUT_SCORE}" 3 PARENT_SCOPE)
        elseif (CHAR STREQUAL "S")
          set("${OUT_SCORE}" 57 PARENT_SCOPE)
        elseif (CHAR STREQUAL "B")
          set("${OUT_SCORE}" 1197 PARENT_SCOPE)
        else()
          set("${OUT_SCORE}" 25137 PARENT_SCOPE)
        endif()
        return()
      endif()
    endif()
  endforeach()
endfunction()

set(SCORE 0)
foreach (LINE IN LISTS LINES)
  scoreCorruptLine("${LINE}" LINE_SCORE)
  math(EXPR SCORE "${SCORE} + ${LINE_SCORE}")
endforeach()

message(STATUS "\tPart 1: ${SCORE}")

# Part 2

function(completeLine LINE OUT_SCORE)
  string(LENGTH "${LINE}" LEN)
  math(EXPR LEN "${LEN} - 1")

  set("${OUT_SCORE}" 0 PARENT_SCOPE)

  foreach (I RANGE 0 ${LEN})
    string(SUBSTRING "${LINE}" ${I} 1 CHAR)
    if (CHAR STREQUAL "p")
      list(APPEND CHUNK_TERM_STACK "P")
    elseif (CHAR STREQUAL "s")
      list(APPEND CHUNK_TERM_STACK "S")
    elseif (CHAR STREQUAL "b")
      list(APPEND CHUNK_TERM_STACK "B")
    elseif (CHAR STREQUAL "a")
      list(APPEND CHUNK_TERM_STACK "A")
    else()
      list(POP_BACK CHUNK_TERM_STACK EXPECTED)
      if (NOT CHAR STREQUAL "${EXPECTED}")
        # Line is corrupt, no score
        return()
      endif()
    endif()
  endforeach()

  set(SCORE 0)

  while (CHUNK_TERM_STACK)
    math(EXPR SCORE "${SCORE} * 5")
    list(POP_BACK CHUNK_TERM_STACK CHAR)
    if (CHAR STREQUAL "P")
      math(EXPR SCORE "${SCORE} + 1")
    elseif (CHAR STREQUAL "S")
      math(EXPR SCORE "${SCORE} + 2")
    elseif (CHAR STREQUAL "B")
      math(EXPR SCORE "${SCORE} + 3")
    else()
      math(EXPR SCORE "${SCORE} + 4")
    endif()
  endwhile()

  set("${OUT_SCORE}" "${SCORE}" PARENT_SCOPE)
endfunction()

foreach (LINE IN LISTS LINES)
  completeLine("${LINE}" LINE_SCORE)

  if (LINE_SCORE GREATER 0)
    list(APPEND AUTO_COMPLETE_SCORES "${LINE_SCORE}")
  endif()
endforeach()

list(SORT AUTO_COMPLETE_SCORES COMPARE NATURAL)
list(LENGTH AUTO_COMPLETE_SCORES LEN)
math(EXPR LEN "${LEN} / 2")
list(GET AUTO_COMPLETE_SCORES ${LEN} PART2_ANSWER)

message(STATUS "\tPart 2: ${PART2_ANSWER}")
