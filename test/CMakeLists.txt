# Fetch test data
IF (EXISTS "${CMAKE_SOURCE_DIR}/.gitmodules")
  FIND_PACKAGE(Git REQUIRED)
  EXECUTE_PROCESS (COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
ENDIF (EXISTS "${CMAKE_SOURCE_DIR}/.gitmodules")

FIND_PACKAGE(Qt5Test REQUIRED)
FIND_PACKAGE(Qt5Xml REQUIRED)
FIND_PACKAGE(Qt5Qml REQUIRED)
FIND_PACKAGE(Qt5Quick REQUIRED)

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_BINARY_DIR}
  ${Qt5Core_INCLUDE_DIRS}
  ${Qt5Test_INCLUDE_DIRS}
  ${Qt5Xml_INCLUDE_DIRS}
  ../src
  ${QGIS_INCLUDE_DIR}/core
  ${QGIS_INCLUDE_DIR}
)


# Required because Qt is compiled with -reduce-relocation
SET (CMAKE_POSITION_INDEPENDENT_CODE TRUE)

ADD_DEFINITIONS ("-DTESTDATA_DIR=\"${CMAKE_CURRENT_SOURCE_DIR}/QGIS-Testdata\"")

MACRO (ADD_UNIT_TEST testname testsrc)
  SET(test_${testname}_SRCS ${testsrc})
  SET(test_${testname}_MOC_CPPS ${testsrc})
  ADD_EXECUTABLE(test_${testname} ${test_${testname}_SRCS})
  SET_TARGET_PROPERTIES(test_${testname} PROPERTIES AUTOMOC TRUE)

  TARGET_LINK_LIBRARIES(test_${testname}
    ${Qt5Core_LIBRARIES}
    ${Qt5Test_LIBRARIES}
    ${Qt5Qml_LIBRARIES}
    ${Qt5Quick_LIBRARIES}
    ${QGIS_CORE_LIBRARY}
    ${QGIS_MOBILE_APP_NAME}_core
  )

  ADD_TEST(qgis_${testname} ${CMAKE_CURRENT_BINARY_DIR}/../output/bin/test_${testname})
ENDMACRO (ADD_UNIT_TEST)

ADD_UNIT_TEST(featuremodel featuremodel.cpp)