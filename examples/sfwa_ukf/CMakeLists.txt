add_library(sfwa_ukf SHARED cukf.cpp)
target_compile_definitions(sfwa_ukf PUBLIC -DUKF_DOUBLE_PRECISION )
target_link_libraries(sfwa_ukf PUBLIC ukf::ukf)
