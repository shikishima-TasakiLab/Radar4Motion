cmake_minimum_required(VERSION 3.8)
project(radar4motion)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(PCL REQUIRED)
find_package(rclcpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(std_msgs REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2 REQUIRED)
find_package(pcl_ros REQUIRED)

find_package(Sophus REQUIRED)
find_package(TBB REQUIRED)

#option(USE_SYSTEM_EIGEN3 "Use system pre-installed Eigen" OFF)
#option(USE_SYSTEM_SOPHUS "Use system pre-installed Sophus" OFF)
#option(USE_SYSTEM_TSL-ROBIN-MAP "Use system pre-installed tsl_robin" OFF)
#option(USE_SYSTEM_TBB "Use system pre-installed oneAPI/tbb" OFF)
#include(3rdparty/find_dependencies.cmake)

include_directories(
  include
  include/radar4motion/kiss-icp
  3rdparty/robin-map/include
  ${PCL_INCLUDE_DIRS}
)

add_executable(radar4motion
  # main
  src/main.cpp
  # kiss-icp
  include/radar4motion/kiss-icp/Deskew.cpp
  include/radar4motion/kiss-icp/Preprocessing.cpp
  include/radar4motion/kiss-icp/Registration.cpp
  include/radar4motion/kiss-icp/Threshold.cpp
  include/radar4motion/kiss-icp/VoxelHashMap.cpp
  include/radar4motion/kiss-icp/VoxelUtils.cpp
)
target_include_directories(radar4motion PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>)
target_compile_features(radar4motion PUBLIC c_std_99 cxx_std_17)  # Require C99 and C++17
ament_target_dependencies(
  radar4motion
  rclcpp
  sensor_msgs
  std_msgs
  Sophus
  tf2_ros
  tf2
  pcl_ros
)
target_link_libraries(radar4motion
  ${PCL_LIBRARIES}
  Sophus::Sophus
  TBB::tbb
)

link_directories(
  ${PCL_LIBRARY_DIRS}
)
add_definitions(
  ${PCL_DEFINITIONS}
)

install(TARGETS radar4motion
  DESTINATION lib/${PROJECT_NAME})

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
