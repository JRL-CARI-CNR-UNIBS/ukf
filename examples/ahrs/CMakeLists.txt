add_library(ahrs SHARED ahrs.cpp)
target_compile_definitions(ahrs PUBLIC -DUKF_DOUBLE_PRECISION )
target_link_libraries(ahrs PUBLIC ukf::ukf)
