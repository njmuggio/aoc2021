# Problem 9: https://adventofcode.com/2021/day/9

set(INPUT_FILE "${CMAKE_CURRENT_SOURCE_DIR}/input")

# Part 1
file(STRINGS "${INPUT_FILE}" RAW_MAP)

list(GET RAW_MAP 0 FIRST_LINE)
string(LENGTH "${FIRST_LINE}" RAW_MAP_WIDTH)
list(LENGTH RAW_MAP RAW_MAP_HEIGHT)

math(EXPR PAD_MAP_WIDTH "${RAW_MAP_WIDTH} + 2")
math(EXPR PAD_MAP_HEIGHT "${RAW_MAP_HEIGHT} + 2")

foreach (X RANGE 1 ${PAD_MAP_WIDTH})
  list(APPEND ROW_0 10)
endforeach()

set(Y 1)
foreach (RAW_LINE IN LISTS RAW_MAP)
  list(APPEND "ROW_${Y}" 10)
  string(LENGTH "${RAW_LINE}" LEN)
  math(EXPR LEN "${LEN} - 1")
  foreach (X RANGE 0 ${LEN})
    string(SUBSTRING "${RAW_LINE}" ${X} 1 HEIGHT)
    list(APPEND "ROW_${Y}" ${HEIGHT})
  endforeach()
  list(APPEND "ROW_${Y}" 10)

  math(EXPR Y "${Y} + 1")
endforeach()

math(EXPR LAST_ROW_IDX "${RAW_MAP_HEIGHT} + 1")
foreach (X RANGE 1 ${PAD_MAP_WIDTH})
  list(APPEND "ROW_${LAST_ROW_IDX}" 10)
endforeach()

set(RISK 0)

foreach (Y RANGE 1 ${RAW_MAP_HEIGHT})
  foreach (X RANGE 1 ${RAW_MAP_WIDTH})
    math(EXPR UP "${Y} - 1")
    math(EXPR DOWN "${Y} + 1")
    math(EXPR LEFT "${X} - 1")
    math(EXPR RIGHT "${X} + 1")

    list(GET "ROW_${UP}" ${X} UP)
    list(GET "ROW_${DOWN}" ${X} DOWN)
    list(GET "ROW_${Y}" ${LEFT} LEFT)
    list(GET "ROW_${Y}" ${RIGHT} RIGHT)

    list(GET "ROW_${Y}" ${X} ME)

    if (ME LESS UP AND ME LESS DOWN AND ME LESS LEFT AND ME LESS RIGHT)
      math(EXPR RISK "${RISK} + ${ME} + 1")
    endif()
  endforeach()
endforeach()

message(STATUS "\tPart 1: ${RISK}")

# Part 2

function(doFloodFill)
  set(SIZE 0)

  while (X_COORDS)
    list(POP_FRONT X_COORDS X)
    list(POP_FRONT Y_COORDS Y)
    math(EXPR SIZE "${SIZE} + 1")

    math(EXPR UP "${Y} - 1")
    list(GET "ROW_${UP}" ${X} UP_VAL)
    if (UP_VAL LESS 9)
      list(APPEND X_COORDS ${X})
      list(APPEND Y_COORDS ${UP})
      list(REMOVE_AT "ROW_${UP}" ${X})
      list(INSERT "ROW_${UP}" ${X} 10)
    endif()

    math(EXPR DOWN "${Y} + 1")
    list(GET "ROW_${DOWN}" ${X} DOWN_VAL)
    if (DOWN_VAL LESS 9)
      list(APPEND X_COORDS ${X})
      list(APPEND Y_COORDS ${DOWN})
      list(REMOVE_AT "ROW_${DOWN}" ${X})
      list(INSERT "ROW_${DOWN}" ${X} 10)
    endif()

    math(EXPR LEFT "${X} - 1")
    list(GET "ROW_${Y}" ${LEFT} LEFT_VAL)
    if (LEFT_VAL LESS 9)
      list(APPEND X_COORDS ${LEFT})
      list(APPEND Y_COORDS ${Y})
      list(REMOVE_AT "ROW_${Y}" ${LEFT})
      list(INSERT "ROW_${Y}" ${LEFT} 10)
    endif()

    math(EXPR RIGHT "${X} + 1")
    list(GET "ROW_${Y}" ${RIGHT} RIGHT_VAL)
    if (RIGHT_VAL LESS 9)
      list(APPEND X_COORDS ${RIGHT})
      list(APPEND Y_COORDS ${Y})
      list(REMOVE_AT "ROW_${Y}" ${RIGHT})
      list(INSERT "ROW_${Y}" ${RIGHT} 10)
    endif()
  endwhile()

  set(BASIN_SIZE "${SIZE}" PARENT_SCOPE)

  foreach (IDX RANGE 1 ${RAW_MAP_HEIGHT})
    set("ROW_${IDX}" "${ROW_${IDX}}" PARENT_SCOPE)
  endforeach()
endfunction()

# Find a low point
foreach (Y RANGE 1 ${RAW_MAP_HEIGHT})
  foreach (X RANGE 1 ${RAW_MAP_WIDTH})
    list(GET "ROW_${Y}" ${X} ME)
    if (ME GREATER_EQUAL 9)
      continue()
    endif()

    math(EXPR UP "${Y} - 1")
    math(EXPR DOWN "${Y} + 1")
    math(EXPR LEFT "${X} - 1")
    math(EXPR RIGHT "${X} + 1")

    list(GET "ROW_${UP}" ${X} UP)
    list(GET "ROW_${DOWN}" ${X} DOWN)
    list(GET "ROW_${Y}" ${LEFT} LEFT)
    list(GET "ROW_${Y}" ${RIGHT} RIGHT)

    if (ME LESS UP AND ME LESS DOWN AND ME LESS LEFT AND ME LESS RIGHT)
      # Found a low point, now flood fill to find basin size
      set(X_COORDS ${X})
      set(Y_COORDS ${Y})

      list(REMOVE_AT "ROW_${Y}" ${X})
      list(INSERT "ROW_${Y}" ${X} 10)

      doFloodFill()
      list(APPEND BASIN_SIZES "${BASIN_SIZE}")
    endif()
  endforeach()
endforeach()

list(SORT BASIN_SIZES COMPARE NATURAL ORDER DESCENDING)
list(POP_FRONT BASIN_SIZES B0)
list(POP_FRONT BASIN_SIZES B1)
list(POP_FRONT BASIN_SIZES B2)

math(EXPR PART2_ANSWER "${B0} * ${B1} * ${B2}")

message(STATUS "\tPart 2: ${PART2_ANSWER}")
