# Problem 8: https://adventofcode.com/2021/day/8

set(INPUT_FILE "${CMAKE_CURRENT_SOURCE_DIR}/input")

# Part 1 - count instances of 1, 4, 7, 8
# 1 - 2 segments
# 4 - 4 segments
# 7 - 3 segments
# 8 - 7 segments

file(STRINGS "${INPUT_FILE}" LINES)

set(COUNT 0)

foreach (LINE IN LISTS LINES)
  string(REPLACE " " ";" LINE "${LINE}")
  foreach (IDX RANGE 11 14)
    list(GET LINE ${IDX} DISPLAY)
    string(LENGTH "${DISPLAY}" LEN)

    if (LEN EQUAL 2 OR LEN EQUAL 3 OR LEN EQUAL 4 OR LEN EQUAL 7)
      math(EXPR COUNT "${COUNT} + 1")
    endif()
  endforeach()
endforeach()

message(STATUS "\tPart 1: ${COUNT}")

# Part 2

set(PART2_ANSWER 0)

function(segIntersect A B OUT_VAR)
  string(REGEX REPLACE "[^${B}]" "" TEMP "${A}")
  set(${OUT_VAR} "${TEMP}" PARENT_SCOPE)
endfunction()

function(segUnion A B OUT_VAR)
  segIntersect("abcdefg" "${A}${B}" TEMP)
  set(${OUT_VAR} "${TEMP}" PARENT_SCOPE)
endfunction()

function(segCmp A B OUT_VAR)
  segIntersect("abcdefg" "${A}" T1)
  segIntersect("abcdefg" "${B}" T2)
  if (T1 STREQUAL T2)
    set(${OUT_VAR} ON PARENT_SCOPE)
  else()
    set(${OUT_VAR} OFF PARENT_SCOPE)
  endif()
endfunction()

macro(printLists)
  message("TOP:        ${TOP}")
  message("MIDDLE:     ${MIDDLE}")
  message("BOTTOM:     ${BOTTOM}")
  message("LEFT_UP:    ${LEFT_UP}")
  message("RIGHT_UP:   ${RIGHT_UP}")
  message("LEFT_DOWN:  ${LEFT_DOWN}")
  message("RIGHT_DOWN: ${RIGHT_DOWN}")
endmacro()

foreach (LINE IN LISTS LINES)
  set(TOP a b c d e f g)
  set(MIDDLE a b c d e f g)
  set(BOTTOM a b c d e f g)
  set(LEFT_UP a b c d e f g)
  set(RIGHT_UP a b c d e f g)
  set(LEFT_DOWN a b c d e f g)
  set(RIGHT_DOWN a b c d e f g)
  set(ALL_SEGMENTS_FOUND OFF)

  set(CHARS_0 "")
  set(CHARS_1 "")
  set(CHARS_2 "")
  set(CHARS_3 "")
  set(CHARS_4 "")
  set(CHARS_5 "")
  set(CHARS_6 "")
  set(CHARS_7 "")
  set(CHARS_8 "")
  set(CHARS_9 "")

  set(NUM_FOUND 0)

  string(REPLACE " " ";" LINE "${LINE}")

  # Need to figure out order of segments for this line
  while (NUM_FOUND LESS 4)
    # Find 1, 4, 7, 8
    foreach (IDX RANGE 0 9)
      list(GET LINE ${IDX} DIGIT)
      string(LENGTH "${DIGIT}" DIGIT_LEN)

      # Segments correspond to '1'
      if (DIGIT_LEN EQUAL 2)
        # message("Found 1: ${DIGIT}")
        set(CHARS_1 "${DIGIT}")

        list(FILTER TOP EXCLUDE REGEX "[${DIGIT}]")
        list(FILTER MIDDLE EXCLUDE REGEX "[${DIGIT}]")
        list(FILTER BOTTOM EXCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_UP EXCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_DOWN EXCLUDE REGEX "[${DIGIT}]")

        list(FILTER RIGHT_UP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_DOWN INCLUDE REGEX "[${DIGIT}]")

        math(EXPR NUM_FOUND "${NUM_FOUND} + 1")

        # printLists()
      endif()

      # Segments correspond to '7'
      if (DIGIT_LEN EQUAL 3)
        # message("Found 7: ${DIGIT}")
        set(CHARS_7 "${DIGIT}")

        list(FILTER MIDDLE EXCLUDE REGEX "[${DIGIT}]")
        list(FILTER BOTTOM EXCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_UP EXCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_DOWN EXCLUDE REGEX "[${DIGIT}]")

        list(FILTER TOP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_UP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_DOWN INCLUDE REGEX "[${DIGIT}]")

        math(EXPR NUM_FOUND "${NUM_FOUND} + 1")

        # printLists()
      endif()

      # Segments correspond to '4'
      if (DIGIT_LEN EQUAL 4)
        # message("Found 4: ${DIGIT}")
        set(CHARS_4 "${DIGIT}")

        list(FILTER TOP EXCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_DOWN EXCLUDE REGEX "[${DIGIT}]")
        list(FILTER BOTTOM EXCLUDE REGEX "[${DIGIT}]")

        list(FILTER MIDDLE INCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_UP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_UP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_DOWN INCLUDE REGEX "[${DIGIT}]")

        math(EXPR NUM_FOUND "${NUM_FOUND} + 1")

        # printLists()
      endif()

      # Segments correspond to '8'
      if (DIGIT_LEN EQUAL 7)
        # message("FOUND 8: ${DIGIT}")
        math(EXPR NUM_FOUND "${NUM_FOUND} + 1")
        set(CHARS_8 "${DIGIT}")
      endif()
    endforeach()
  endwhile()

  # At this point, we know TOP, and have filtered the others down a bit.
  # So far, we have 1, 4, 7, 8

  # Find '6'
  foreach (IDX RANGE 0 9)
    list(GET LINE ${IDX} DIGIT)
    string(LENGTH "${DIGIT}" DIGIT_LEN)

    if (DIGIT_LEN EQUAL 6)
      segUnion("${DIGIT}" "${CHARS_1}" UNION)
      segCmp("${UNION}" "${CHARS_8}" MATCH)
      if (MATCH)
        # message("Found 6: ${DIGIT}")
        set(CHARS_6 "${DIGIT}")

        list(FILTER RIGHT_UP EXCLUDE REGEX "[${DIGIT}]")

        list(FILTER TOP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER MIDDLE INCLUDE REGEX "[${DIGIT}]")
        list(FILTER BOTTOM INCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_UP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_DOWN INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_DOWN INCLUDE REGEX "[${DIGIT}]")

        math(EXPR NUM_FOUND "${NUM_FOUND} + 1")

        # printLists()
        break()
      endif()
    endif()
  endforeach()

  # So far, we have 1, 4, 6, 7, 8

  # Find '0' and '9'
  foreach (IDX RANGE 0 9)
    list(GET LINE ${IDX} DIGIT)
    string(LENGTH "${DIGIT}" DIGIT_LEN)

    if (DIGIT_LEN EQUAL 6)
      if (DIGIT STREQUAL CHARS_6)
        continue()
      endif()

      segUnion("${DIGIT}" "${CHARS_4}" UNION)
      segCmp("${UNION}" "${DIGIT}" MATCH)

      if (MATCH)
        # message("Found 9: ${DIGIT}")
        set(CHARS_9 "${DIGIT}")

        list(FILTER LEFT_DOWN EXCLUDE REGEX "[${DIGIT}]")

        list(FILTER TOP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER MIDDLE INCLUDE REGEX "[${DIGIT}]")
        list(FILTER BOTTOM INCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_UP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_UP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_DOWN INCLUDE REGEX "[${DIGIT}]")

        math(EXPR NUM_FOUND "${NUM_FOUND} + 1")

        # printLists()
      else()
        # message("Found 0: ${DIGIT}")
        set(CHARS_0 "${DIGIT}")

        list(FILTER MIDDLE EXCLUDE REGEX "[${DIGIT}]")

        list(FILTER TOP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER BOTTOM INCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_UP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_DOWN INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_UP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_DOWN INCLUDE REGEX "[${DIGIT}]")

        math(EXPR NUM_FOUND "${NUM_FOUND} + 1")

        # printLists()
      endif()
    endif()
  endforeach()

  # So far, we have 0, 1, 4, 6, 7, 8, 9

  # Find '2'
  foreach (IDX RANGE 0 9)
    list(GET LINE ${IDX} DIGIT)
    string(LENGTH "${DIGIT}" DIGIT_LEN)

    if (DIGIT_LEN EQUAL 5)
      segUnion("${DIGIT}" "${CHARS_4}" UNION)
      segCmp("${UNION}" "${CHARS_8}" MATCH)

      if (MATCH)
        # message("Found 2: ${DIGIT}")
        set(CHARS_2 "${DIGIT}")

        list(FILTER LEFT_UP EXCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_DOWN EXCLUDE REGEX "[${DIGIT}]")

        list(FILTER TOP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER MIDDLE INCLUDE REGEX "[${DIGIT}]")
        list(FILTER BOTTOM INCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_DOWN INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_UP INCLUDE REGEX "[${DIGIT}]")

        math(EXPR NUM_FOUND "${NUM_FOUND} + 1")

        # printLists()
      endif()
    endif()
  endforeach()

  # So far, we have 0, 1, 2, 4, 6, 7, 8, 9

  # Find '3' and '5'
  foreach (IDX RANGE 0 9)
    list(GET LINE ${IDX} DIGIT)
    string(LENGTH "${DIGIT}" DIGIT_LEN)

    if (DIGIT_LEN EQUAL 5)
      if (DIGIT STREQUAL CHARS_2)
        continue()
      endif()

      segUnion("${DIGIT}" "${CHARS_1}" UNION)
      segCmp("${UNION}" "${CHARS_9}" MATCH)

      if (MATCH)
        # message("Found 5: ${DIGIT}")
        set(CHARS_5 "${DIGIT}")

        list(FILTER LEFT_DOWN EXCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_UP EXCLUDE REGEX "[${DIGIT}]")

        list(FILTER TOP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER MIDDLE INCLUDE REGEX "[${DIGIT}]")
        list(FILTER BOTTOM INCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_UP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_DOWN INCLUDE REGEX "[${DIGIT}]")

        math(EXPR NUM_FOUND "${NUM_FOUND} + 1")

        # printLists()
      else()
        # message("Found 3: ${DIGIT}")
        set(CHARS_3 "${DIGIT}")

        list(FILTER LEFT_UP EXCLUDE REGEX "[${DIGIT}]")
        list(FILTER LEFT_DOWN EXCLUDE REGEX "[${DIGIT}]")

        list(FILTER TOP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER MIDDLE INCLUDE REGEX "[${DIGIT}]")
        list(FILTER BOTTOM INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_UP INCLUDE REGEX "[${DIGIT}]")
        list(FILTER RIGHT_DOWN INCLUDE REGEX "[${DIGIT}]")

        math(EXPR NUM_FOUND "${NUM_FOUND} + 1")

        # printLists()
      endif()
    endif()
  endforeach()

  set(DISPLAY_VALUE "")
  # Figure out the four digits
  foreach (NUM RANGE 11 14)
    list(GET LINE ${NUM} DIGIT)
    foreach (VAL RANGE 0 9)
      segCmp("${CHARS_${VAL}}" "${DIGIT}" MATCH)
      if (MATCH)
        string(APPEND DISPLAY_VALUE "${VAL}")
      endif()
    endforeach()
  endforeach()

  math(EXPR PART2_ANSWER "${PART2_ANSWER} + ${DISPLAY_VALUE}")
endforeach()

message(STATUS "\tPart 2: ${PART2_ANSWER}")
