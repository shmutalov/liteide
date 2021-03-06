##### Probably don't want to edit below this line #####

SET( QT_USE_QTTEST TRUE )

# Use it
INCLUDE( ${QT_USE_FILE} )

INCLUDE(AddFileDependencies)

# Include the library include directories, and the current build directory (moc)
INCLUDE_DIRECTORIES(
  ../../include
  ${CMAKE_CURRENT_BINARY_DIR}
)

SET (qjson_test_support_SRCS person.cpp)
QT4_WRAP_CPP(qjson_test_support_MOC_SRCS person.h)

ADD_LIBRARY (qjson_test_support STATIC ${qjson_test_support_SRCS}
                                       ${qjson_test_support_MOC_SRCS})

SET( UNIT_TESTS
  testqobjecthelper
)

# Build the tests
FOREACH(test ${UNIT_TESTS})
  MESSAGE(STATUS "Building ${test}")
  QT4_WRAP_CPP(MOC_SOURCE ${test}.cpp)
  ADD_EXECUTABLE(
    ${test}
    ${test}.cpp
  )

  ADD_FILE_DEPENDENCIES(${test}.cpp ${MOC_SOURCE})
  TARGET_LINK_LIBRARIES(
    ${test}
    ${QT_LIBRARIES}
    qjson
    qjson_test_support
  )
  if (QJSON_TEST_OUTPUT STREQUAL "xml")
    # produce XML output
    add_test( ${test} ${test} -xml -o ${test}.tml )
  else (QJSON_TEST_OUTPUT STREQUAL "xml")
    add_test( ${test} ${test} )
  endif (QJSON_TEST_OUTPUT STREQUAL "xml")
ENDFOREACH()
