
# Disable dynamic memory allocation in Eigen
#ADD_DEFINITIONS(-DEIGEN_NO_MALLOC -DUKF_SINGLE_PRECISION)

#INCLUDE_DIRECTORIES(../../include ${eigen_dir})

#cmake_policy(SET CMP0042 NEW)

add_library(ahrs SHARED ahrs.cpp)
#set_target_properties(ahrs PROPERTIES OUTPUT_NAME ahrs)
target_link_libraries(ahrs PUBLIC ukf::ukf)
