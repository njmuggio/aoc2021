# Problem 4: https://adventofcode.com/2021/day/4

set(INPUT_FILE "${CMAKE_CURRENT_SOURCE_DIR}/input")

# Part 1 - Find the first board to win
file(STRINGS "${INPUT_FILE}" LINES)

list(POP_FRONT LINES NUMS_TO_CALL)
string(REPLACE "," ";" NUMS_TO_CALL "${NUMS_TO_CALL}")

list(POP_FRONT LINES) # Remove empty line after calls

set(BOARD_IDX 0)

# Load the boards, one board per list
while(LINES)
  list(POP_FRONT LINES LINE1)
  list(POP_FRONT LINES LINE2)
  list(POP_FRONT LINES LINE3)
  list(POP_FRONT LINES LINE4)
  list(POP_FRONT LINES LINE5)
  list(POP_FRONT LINES) # Remove empty line

  string(REPLACE " " ";" "BOARD${BOARD_IDX}" "${LINE1} ${LINE2} ${LINE3} ${LINE4} ${LINE5}")
  list(FILTER "BOARD${BOARD_IDX}" EXCLUDE REGEX "^$")
  list(APPEND BOARD_LIST "BOARD${BOARD_IDX}")
  math(EXPR BOARD_IDX "${BOARD_IDX} + 1")
endwhile()

set(WINNER -1)

while(WINNER EQUAL -1)
  # Call the next number
  list(POP_FRONT NUMS_TO_CALL NUM)

  foreach(BOARD_NAME IN LISTS BOARD_LIST)
    # Replace matches with -1
    list(TRANSFORM "${BOARD_NAME}" REPLACE ".+" "-1" REGEX "^${NUM}$")

    # Test columns
    foreach(COL RANGE 0 5)
      set(WINNER_FOUND ON)

      foreach(CELL RANGE ${COL} 24 5)
        list(GET "${BOARD_NAME}" ${CELL} VAL)
        if(NOT VAL EQUAL -1)
          set(WINNER_FOUND OFF)
          break()
        endif()
      endforeach()

      if (WINNER_FOUND)
        break()
      endif()
    endforeach()

    if(NOT WINNER_FOUND)
      # Test rows
      foreach(ROW RANGE 0 4)
        set(WINNER_FOUND ON)

        math(EXPR ROW_START "${ROW} * 5")
        math(EXPR ROW_END "${ROW_START} + 4")
        foreach(CELL RANGE ${ROW_START} ${ROW_END})
          list(GET "${BOARD_NAME}" ${CELL} VAL)
          if(NOT VAL EQUAL -1)
            set(WINNER_FOUND OFF)
            break()
          endif()
        endforeach()

        if (WINNER_FOUND)
          break()
        endif()
      endforeach()
    endif()

    if (WINNER_FOUND)
      set(WINNER "${BOARD_NAME}")
      break()
    endif()
  endforeach()
endwhile()

set(SUM 0)
while (${WINNER})
  list(POP_FRONT "${WINNER}" VAL)
  if(NOT VAL EQUAL -1)
    math(EXPR SUM "${SUM} + ${VAL}")
  endif()
endwhile()

math(EXPR PART1_ANSWER "${SUM} * ${NUM}")

message(STATUS "\tPart 1: ${PART1_ANSWER}")

# Part 2 - Find the last board to win
file(STRINGS "${INPUT_FILE}" LINES)

list(POP_FRONT LINES NUMS_TO_CALL)
string(REPLACE "," ";" NUMS_TO_CALL "${NUMS_TO_CALL}")

list(POP_FRONT LINES) # Remove empty line after calls

set(BOARD_IDX 0)
set(BOARD_LIST "")

# Load the boards, one board per list
while(LINES)
  list(POP_FRONT LINES LINE1)
  list(POP_FRONT LINES LINE2)
  list(POP_FRONT LINES LINE3)
  list(POP_FRONT LINES LINE4)
  list(POP_FRONT LINES LINE5)
  list(POP_FRONT LINES) # Remove empty line

  string(REPLACE " " ";" "BOARD${BOARD_IDX}" "${LINE1} ${LINE2} ${LINE3} ${LINE4} ${LINE5}")
  list(FILTER "BOARD${BOARD_IDX}" EXCLUDE REGEX "^$")
  list(APPEND BOARD_LIST "BOARD${BOARD_IDX}")
  math(EXPR BOARD_IDX "${BOARD_IDX} + 1")
endwhile()

set(WINNER -1)

while(NUMS_TO_CALL AND BOARD_LIST)
  # Call the next number
  list(POP_FRONT NUMS_TO_CALL NUM)

  foreach(BOARD_NAME IN LISTS BOARD_LIST)
    # Replace matches with -1
    list(TRANSFORM "${BOARD_NAME}" REPLACE ".+" "-1" REGEX "^${NUM}$")
  endforeach()

  foreach(BOARD_NAME IN LISTS BOARD_LIST)
    # Test columns
    foreach(COL RANGE 0 5)
      set(WINNER_FOUND ON)

      foreach(CELL RANGE ${COL} 24 5)
        list(GET "${BOARD_NAME}" ${CELL} VAL)
        if(NOT VAL EQUAL -1)
          set(WINNER_FOUND OFF)
          break()
        endif()
      endforeach()

      if (WINNER_FOUND)
        break()
      endif()
    endforeach()

    if(NOT WINNER_FOUND)
      # Test rows
      foreach(ROW RANGE 0 4)
        set(WINNER_FOUND ON)

        math(EXPR ROW_START "${ROW} * 5")
        math(EXPR ROW_END "${ROW_START} + 4")
        foreach(CELL RANGE ${ROW_START} ${ROW_END})
          list(GET "${BOARD_NAME}" ${CELL} VAL)
          if(NOT VAL EQUAL -1)
            set(WINNER_FOUND OFF)
            break()
          endif()
        endforeach()

        if (WINNER_FOUND)
          break()
        endif()
      endforeach()
    endif()

    if (WINNER_FOUND)
      set(PREV_WINNER "${BOARD_NAME}")
      set(LAST_WIN_NUM "${NUM}")
      list(REMOVE_ITEM BOARD_LIST "${PREV_WINNER}")
      set(WINNER "${BOARD_NAME}")
    endif()
  endforeach()
endwhile()

set(SUM 0)
while (${PREV_WINNER})
  list(POP_FRONT "${PREV_WINNER}" VAL)
  if(NOT VAL EQUAL -1)
    math(EXPR SUM "${SUM} + ${VAL}")
  endif()
endwhile()

math(EXPR PART2_ANSWER "${SUM} * ${LAST_WIN_NUM}")

message(STATUS "\tPart 2: ${PART2_ANSWER}")
