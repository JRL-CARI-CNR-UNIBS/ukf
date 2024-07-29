ADD_EXECUTABLE(unittest
  TestStateVector.cpp
  TestFixedMeasurementVector.cpp
  TestDynamicMeasurementVector.cpp
  TestCore.cpp
  TestSquareRootCore.cpp)

target_link_libraries(unittest PUBLIC ukf::ukf GTest::gtest_main)

target_compile_definitions(unittest PUBLIC -DUKF_DOUBLE_PRECISION )
