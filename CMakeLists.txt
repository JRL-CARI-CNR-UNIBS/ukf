CMAKE_MINIMUM_REQUIRED(VERSION 3.16 FATAL_ERROR)
PROJECT(ukf)
#INCLUDE(ExternalProject)
#INCLUDE(CheckCXXCompilerFlag)

# Check for C++14 support.
# CHECK_CXX_COMPILER_FLAG("-std=c++14" COMPILER_SUPPORTS_CXX14)
# IF(COMPILER_SUPPORTS_CXX14)
#     SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14")
# ELSE()
#     MESSAGE(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++14 support. Please use a different C++ compiler.")
# ENDIF()
#
# SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-deprecated-register -Wno-ignored-attributes")
#
# # Set default ExternalProject root directory
# SET_DIRECTORY_PROPERTIES(PROPERTIES EP_PREFIX .)

# # Add eigen3.2.8
# ExternalProject_Add(
# eigen3
# URL https://gitlab.com/libeigen/eigen/-/archive/3.2.8/eigen-3.2.8.tar.gz
# TIMEOUT 30
# # Apply the LLT malloc fix patch, necessary to avoid dynamic memory allocation on embedded systems.
# # This can be omitted on systems where dynamic memory allocation is acceptable.
# PATCH_COMMAND patch -p1 -t -N < ${PROJECT_SOURCE_DIR}/patch/eigen-3.2.8-no-malloc-in-llt.patch
# # Disable install step
# INSTALL_COMMAND ""
# # Wrap download, configure and build steps in a script to log output
# LOG_DOWNLOAD ON
# LOG_CONFIGURE ON
# LOG_BUILD ON)

# ExternalProject_Get_Property(eigen3 source_dir)
# SET(eigen_dir ${source_dir})
#
# INCLUDE_DIRECTORIES(${eigen_dir})

# GET_DIRECTORY_PROPERTY(hasParent PARENT_DIRECTORY)
# IF(hasParent)
#     SET(eigen_dir ${eigen_dir} PARENT_SCOPE)
# ENDIF()
find_package(Eigen3 REQUIRED COMPONENTS Core Dense  Geometry)

add_library(ukf INTERFACE)
target_include_directories(ukf
  INTERFACE
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/include> #if BUILD_INTERFACE, add ${CMAKE_SOURCE_DIR}/include (headers taken by source directory)
    $<INSTALL_INTERFACE:include> # if INSTALL_INTERFACE, add header from install path
  )
target_compile_definitions(ukf
  INTERFACE
  )
target_link_libraries(ukf INTERFACE Eigen3::Eigen)
target_compile_definitions(ukf INTERFACE -DEIGEN_NO_MALLOC -DUKF_DOUBLE_PRECISION )
add_library(ukf::ukf ALIAS ukf)
target_compile_features(ukf INTERFACE cxx_std_17)


ENABLE_TESTING()

#if(ENABLE_TESTING)
  find_package(GTest REQUIRED)
  include(GoogleTest)
  include(CTest)
#endif()

ADD_SUBDIRECTORY(test)
# ADD_SUBDIRECTORY(benchmark EXCLUDE_FROM_ALL)
add_subdirectory(examples)



### #############
### ## Install ##
### #############


install(DIRECTORY include/${PROJECT_NAME}
  DESTINATION include)

install(
  TARGETS ${PROJECT_NAME}
  EXPORT ${PROJECT_NAME}Targets
  ARCHIVE DESTINATION lib/${PROJECT_NAME}
  LIBRARY DESTINATION lib/${PROJECT_NAME}
  RUNTIME DESTINATION bin/${PROJECT_NAME}
)

install(EXPORT ${PROJECT_NAME}Targets
  DESTINATION "share/${PROJECT_NAME}/cmake"
  NAMESPACE ${PROJECT_NAME}::
  FILE ${PROJECT_NAME}Targets.cmake
)


include(CMakePackageConfigHelpers)
write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake
  VERSION 0.0.1
  COMPATIBILITY AnyNewerVersion)

install(FILES
  ${CMAKE_SOURCE_DIR}/cmake_config/${PROJECT_NAME}Config.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake
  DESTINATION "share/${PROJECT_NAME}/cmake")


# make uninstall
add_custom_target("uninstall" COMMENT "Uninstall installed files")
add_custom_command(
    TARGET "uninstall"
    POST_BUILD
    COMMENT "Uninstall files with install_manifest.txt"
    COMMAND xargs rm -vf < install_manifest.txt || echo Nothing in
            install_manifest.txt to be uninstalled!
)
