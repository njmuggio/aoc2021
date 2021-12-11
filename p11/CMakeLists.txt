# Problem 11: https://adventofcode.com/2021/day/11

set(INPUT_FILE "${CMAKE_CURRENT_SOURCE_DIR}/input")

# Part 1
file(STRINGS "${INPUT_FILE}" LINES)

list(LENGTH LINES MAP_HEIGHT)
list(GET LINES 0 FIRST_LINE)
string(LENGTH "${FIRST_LINE}" MAP_WIDTH)

set(ROW 0)
foreach (LINE IN LISTS LINES)
  list(GET LINES ${ROW} LINE)
  string(LENGTH "${LINE}" LEN)
  math(EXPR LEN "${LEN} - 1")
  foreach (I RANGE 0 ${LEN})
    string(SUBSTRING "${LINE}" ${I} 1 CHAR)
    list(APPEND "ROW_${ROW}" "${CHAR}")
  endforeach()
  math(EXPR ROW "${ROW} + 1")
endforeach()

math(EXPR LAST_ROW_IDX "${MAP_HEIGHT} - 1")
math(EXPR LAST_CELL_IDX "${MAP_WIDTH} - 1")

set(FLASH_COUNT 0)

foreach (DAY RANGE 1 100)
  set(X_COORDS "")
  set(Y_COORDS "")

  foreach (ROW RANGE 0 ${LAST_ROW_IDX})
    foreach (CELL RANGE 0 ${LAST_CELL_IDX})
      list(GET "ROW_${ROW}" ${CELL} VAL)
      list(REMOVE_AT "ROW_${ROW}" ${CELL})
      math(EXPR VAL "${VAL} + 1")

      if (VAL GREATER 9)
        list(APPEND X_COORDS "${CELL}")
        list(APPEND Y_COORDS "${ROW}")
        list(INSERT "ROW_${ROW}" ${CELL} -1000)
      else()
        list(INSERT "ROW_${ROW}" ${CELL} ${VAL})
      endif()
    endforeach()
  endforeach()

  list(LENGTH X_COORDS LEN)
  if (LEN EQUAL 0)
    set(DONE ON)
  else()
    set(DONE OFF)
  endif()

  while (NOT DONE)
    list(POP_FRONT X_COORDS CELL)
    list(POP_FRONT Y_COORDS ROW)
    math(EXPR FLASH_COUNT "${FLASH_COUNT} + 1")

    foreach (Y_OFF RANGE -1 1)
      math(EXPR Y "${ROW} + ${Y_OFF}")

      if (Y LESS 0 OR Y EQUAL MAP_HEIGHT)
        continue()
      endif()

      foreach (X_OFF RANGE -1 1)
        if (Y_OFF EQUAL 0 AND X_OFF EQUAL 0)
          continue()
        endif()

        math(EXPR X "${CELL} + ${X_OFF}")

        if (X LESS 0 OR X EQUAL MAP_WIDTH)
          continue()
        endif()

        list(GET "ROW_${Y}" ${X} NEIGHBOR_VAL)
        math(EXPR NEIGHBOR_VAL "${NEIGHBOR_VAL} + 1")

        list(REMOVE_AT "ROW_${Y}" ${X})

        if (NEIGHBOR_VAL GREATER 9)
          list(INSERT "ROW_${Y}" ${X} -1000)
          list(APPEND X_COORDS ${X})
          list(APPEND Y_COORDS ${Y})
        else()
          list(INSERT "ROW_${Y}" ${X} ${NEIGHBOR_VAL})
        endif()
      endforeach()
    endforeach()

    list(LENGTH X_COORDS LEN)
    if (LEN EQUAL 0)
      set(DONE ON)
    endif()
  endwhile()

  foreach (ROW RANGE 0 ${LAST_ROW_IDX})
    list(TRANSFORM "ROW_${ROW}" REPLACE "-[0-9]+" "0")
  endforeach()
endforeach()

message(STATUS "\tPart 1: ${FLASH_COUNT}")

# Part 2

file(STRINGS "${INPUT_FILE}" LINES)

list(LENGTH LINES MAP_HEIGHT)
list(GET LINES 0 FIRST_LINE)
string(LENGTH "${FIRST_LINE}" MAP_WIDTH)

math(EXPR LAST_ROW_IDX "${MAP_HEIGHT} - 1")
foreach (I RANGE 0 ${LAST_ROW_IDX})
  set("ROW_${I}" "")
endforeach()

set(ROW 0)
foreach (LINE IN LISTS LINES)
  list(GET LINES ${ROW} LINE)
  string(LENGTH "${LINE}" LEN)
  math(EXPR LEN "${LEN} - 1")
  foreach (I RANGE 0 ${LEN})
    string(SUBSTRING "${LINE}" ${I} 1 CHAR)
    list(APPEND "ROW_${ROW}" "${CHAR}")
  endforeach()
  math(EXPR ROW "${ROW} + 1")
endforeach()

math(EXPR LAST_ROW_IDX "${MAP_HEIGHT} - 1")
math(EXPR LAST_CELL_IDX "${MAP_WIDTH} - 1")

set(FLASH_COUNT 0)
math(EXPR TARGET_FLASH_COUNT "${MAP_WIDTH} * ${MAP_HEIGHT}")

set(DAY 0)

while (NOT FLASH_COUNT EQUAL TARGET_FLASH_COUNT)
  set(FLASH_COUNT 0)
  math(EXPR DAY "${DAY} + 1")
  set(X_COORDS "")
  set(Y_COORDS "")

  foreach (ROW RANGE 0 ${LAST_ROW_IDX})
    foreach (CELL RANGE 0 ${LAST_CELL_IDX})
      list(GET "ROW_${ROW}" ${CELL} VAL)
      list(REMOVE_AT "ROW_${ROW}" ${CELL})
      math(EXPR VAL "${VAL} + 1")

      if (VAL GREATER 9)
        list(APPEND X_COORDS "${CELL}")
        list(APPEND Y_COORDS "${ROW}")
        list(INSERT "ROW_${ROW}" ${CELL} -1000)
      else()
        list(INSERT "ROW_${ROW}" ${CELL} ${VAL})
      endif()
    endforeach()
  endforeach()

  list(LENGTH X_COORDS LEN)
  if (LEN EQUAL 0)
    set(DONE ON)
  else()
    set(DONE OFF)
  endif()

  while (NOT DONE)
    list(POP_FRONT X_COORDS CELL)
    list(POP_FRONT Y_COORDS ROW)
    math(EXPR FLASH_COUNT "${FLASH_COUNT} + 1")

    foreach (Y_OFF RANGE -1 1)
      math(EXPR Y "${ROW} + ${Y_OFF}")

      if (Y LESS 0 OR Y EQUAL MAP_HEIGHT)
        continue()
      endif()

      foreach (X_OFF RANGE -1 1)
        if (Y_OFF EQUAL 0 AND X_OFF EQUAL 0)
          continue()
        endif()

        math(EXPR X "${CELL} + ${X_OFF}")

        if (X LESS 0 OR X EQUAL MAP_WIDTH)
          continue()
        endif()

        list(GET "ROW_${Y}" ${X} NEIGHBOR_VAL)
        math(EXPR NEIGHBOR_VAL "${NEIGHBOR_VAL} + 1")

        list(REMOVE_AT "ROW_${Y}" ${X})

        if (NEIGHBOR_VAL GREATER 9)
          list(INSERT "ROW_${Y}" ${X} -1000)
          list(APPEND X_COORDS ${X})
          list(APPEND Y_COORDS ${Y})
        else()
          list(INSERT "ROW_${Y}" ${X} ${NEIGHBOR_VAL})
        endif()
      endforeach()
    endforeach()

    list(LENGTH X_COORDS LEN)
    if (LEN EQUAL 0)
      set(DONE ON)
    endif()
  endwhile()

  foreach (ROW RANGE 0 ${LAST_ROW_IDX})
    list(TRANSFORM "ROW_${ROW}" REPLACE "-[0-9]+" "0")
  endforeach()
endwhile()

message(STATUS "\tPart 2: ${DAY}")
