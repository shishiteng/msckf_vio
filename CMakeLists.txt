cmake_minimum_required(VERSION 2.8.3)
project(msckf_vio)


set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

# Modify cmake module path if new .cmake files are required
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_LIST_DIR}/cmake")

find_package(catkin REQUIRED COMPONENTS
  roscpp
  std_msgs
  tf
  nav_msgs
  sensor_msgs
  geometry_msgs
  eigen_conversions
  tf_conversions
  random_numbers
  message_generation
  image_transport
  cv_bridge
  message_filters
  pcl_conversions
  pcl_ros
  camera_model
)


## System dependencies are found with CMake's conventions
find_package(Boost REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(OpenCV REQUIRED)
find_package(Cholmod REQUIRED)
find_package(SPQR REQUIRED)
find_package(Ceres REQUIRED)

add_message_files(
  FILES

  FeatureMeasurement.msg
  CameraMeasurement.msg
  TrackingInfo.msg

  #DebugImuState.msg
  #DebugCamState.msg
  #DebugState.msg
  #DebugMsckfInfo.msg
)

generate_messages(
  DEPENDENCIES
  std_msgs
)

catkin_package(
  INCLUDE_DIRS include
#  LIBRARIES msckf_vio
  CATKIN_DEPENDS
    roscpp std_msgs tf nav_msgs sensor_msgs geometry_msgs
    eigen_conversions tf_conversions message_runtime
    image_transport cv_bridge message_filters pcl_conversions
    pcl_ros std_srvs
  DEPENDS
    Boost EIGEN3 OpenCV
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIR}
  ${CERES_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIR}
  ${OpenCV_INCLUDE_DIRS}
)

link_directories(
  ${catkin_LIBRARY_DIRS}
)

# Msckf Vio
add_library(msckf_vio
  src/msckf_vio.cpp
)
add_dependencies(msckf_vio
  ${${PROJECT_NAME}_EXPORTED_TARGETS}
  ${catkin_EXPORTED_TARGETS}
)
target_link_libraries(msckf_vio
  ${catkin_LIBRARIES}
  ${CERES_LIBRARIES}
)

# Msckf Vio nodelet
add_library(msckf_vio_nodelet
  src/msckf_vio_nodelet.cpp
)
add_dependencies(msckf_vio_nodelet
  ${${PROJECT_NAME}_EXPORTED_TARGETS}
  ${catkin_EXPORTED_TARGETS}
)
target_link_libraries(msckf_vio_nodelet
  msckf_vio
  ${CERES_LIBRARIES}
  ${catkin_LIBRARIES}
/usr/local/lib/libamd.so
/usr/local/lib/libbtf.so
/usr/local/lib/libcamd.so
/usr/local/lib/libccolamd.so
/usr/local/lib/libcholmod.so
/usr/local/lib/libcxsparse.so
/usr/local/lib/libklu.so
/usr/local/lib/libldl.so
/usr/local/lib/libmetis.so
/usr/local/lib/librbio.so
/usr/local/lib/libspqr.so
/usr/local/lib/libsuitesparseconfig.so
/usr/local/lib/libumfpack.so
)

# Image processor
add_library(image_processor
  src/image_processor.cpp
)
add_dependencies(image_processor
  ${${PROJECT_NAME}_EXPORTED_TARGETS}
  ${catkin_EXPORTED_TARGETS}
)
target_link_libraries(image_processor
  ${catkin_LIBRARIES}
  ${OpenCV_LIBRARIES}
)

# Image processor nodelet
add_library(image_processor_nodelet
  src/image_processor_nodelet.cpp
)
add_dependencies(image_processor_nodelet
  ${${PROJECT_NAME}_EXPORTED_TARGETS}
  ${catkin_EXPORTED_TARGETS}
)
target_link_libraries(image_processor_nodelet
  image_processor
  ${catkin_LIBRARIES}
)

# Feature initialization test
catkin_add_gtest(test_feature_init
  test/feature_initialization_test.cpp
)
add_dependencies(test_feature_init
  ${${PROJECT_NAME}_EXPORTED_TARGETS}
  ${catkin_EXPORTED_TARGETS}
)
target_link_libraries(test_feature_init
  ${catkin_LIBRARIES}
)

# Math utils test
catkin_add_gtest(test_math_utils
  test/math_utils_test.cpp
)
