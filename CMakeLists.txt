# cmake_minimum_required(VERSION 3.5)
# project(orbslam3)

# # You should set the PYTHONPATH to your own python site-packages path
# set(ENV{PYTHONPATH} "/opt/ros/humble/lib/python3.10/site-packages/")

# set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/CMakeModules)

# # Default to C++14
# if(NOT CMAKE_CXX_STANDARD)
#   set(CMAKE_CXX_STANDARD 14)
# endif()

# if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
#   add_compile_options(-Wall -Wextra -Wpedantic)
# endif()

# set(OpenCV_DIR "/usr/local/lib/cmake/opencv4")
# find_package(OpenCV REQUIRED)
# find_package(ament_cmake REQUIRED)
# find_package(rclcpp REQUIRED)
# find_package(sensor_msgs REQUIRED)
# find_package(cv_bridge REQUIRED)
# find_package(message_filters REQUIRED)
# find_package(Sophus REQUIRED)
# find_package(Pangolin REQUIRED)
# find_package(ORB_SLAM3 REQUIRED)

# include_directories(
#   include
#   ${ORB_SLAM3_ROOT_DIR}/include
#   ${ORB_SLAM3_ROOT_DIR}/include/CameraModels
# )

# link_directories(
#   include
# )

# add_executable(mono
#   src/monocular/mono.cpp
#   src/monocular/monocular-slam-node.cpp
# )
# ament_target_dependencies(mono rclcpp sensor_msgs cv_bridge ORB_SLAM3 Pangolin)

# add_executable(rgbd
#   src/rgbd/rgbd.cpp
#   src/rgbd/rgbd-slam-node.cpp
# )
# ament_target_dependencies(rgbd rclcpp sensor_msgs cv_bridge message_filters ORB_SLAM3 Pangolin)

# add_executable(stereo
#   src/stereo/stereo.cpp
#   src/stereo/stereo-slam-node.cpp
# )
# ament_target_dependencies(stereo rclcpp sensor_msgs cv_bridge message_filters
# ORB_SLAM3 Pangolin)

# add_executable(stereo-inertial
#   src/stereo-inertial/stereo-inertial.cpp
#   src/stereo-inertial/stereo-inertial-node.cpp
# )
# ament_target_dependencies(stereo-inertial rclcpp sensor_msgs cv_bridge ORB_SLAM3 Pangolin)

# install(TARGETS mono rgbd stereo stereo-inertial
#   DESTINATION lib/${PROJECT_NAME})

# # Install launch files.
# #install(DIRECTORY launch config vocabulary
# #  DESTINATION share/${PROJECT_NAME}/)

# ament_package()



cmake_minimum_required(VERSION 3.5)
project(orbslam3)

# 设置 Python 路径（按需保留）
set(ENV{PYTHONPATH} "/opt/ros/humble/lib/python3.10/site-packages/")

# 添加自定义模块路径
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/CMakeModules)

# 使用 C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# === ROS2 和依赖库 ===
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(message_filters REQUIRED)
find_package(Sophus REQUIRED)
find_package(Pangolin REQUIRED)
find_package(ORB_SLAM3 REQUIRED)

# === 指定 OpenCV 4.4.0 ===
set(OpenCV_DIR "/usr/local/lib/cmake/opencv4")  # 按你的 OpenCV 4.4 安装路径设置
find_package(OpenCV REQUIRED)

# 手动追加需要的 OpenCV 模块
list(APPEND OpenCV_LIBS opencv_calib3d opencv_core opencv_imgproc opencv_highgui)

message(STATUS "OpenCV version: ${OpenCV_VERSION}")
message(STATUS "OpenCV libs: ${OpenCV_LIBS}")

# === 包含路径 ===
include_directories(
  include
  ${ORB_SLAM3_ROOT_DIR}/include
  ${ORB_SLAM3_ROOT_DIR}/include/CameraModels
)

link_directories(
  include
)

# === 可执行文件及其依赖 ===
add_executable(mono
  src/monocular/mono.cpp
  src/monocular/monocular-slam-node.cpp
)
ament_target_dependencies(mono rclcpp sensor_msgs cv_bridge ORB_SLAM3 Pangolin)
target_link_libraries(mono ${OpenCV_LIBS})

add_executable(rgbd
  src/rgbd/rgbd.cpp
  src/rgbd/rgbd-slam-node.cpp
)
ament_target_dependencies(rgbd rclcpp sensor_msgs cv_bridge message_filters ORB_SLAM3 Pangolin)
target_link_libraries(rgbd ${OpenCV_LIBS})

add_executable(stereo
  src/stereo/stereo.cpp
  src/stereo/stereo-slam-node.cpp
)
ament_target_dependencies(stereo rclcpp sensor_msgs cv_bridge message_filters ORB_SLAM3 Pangolin)
target_link_libraries(stereo ${OpenCV_LIBS})

add_executable(stereo-inertial
  src/stereo-inertial/stereo-inertial.cpp
  src/stereo-inertial/stereo-inertial-node.cpp
)
ament_target_dependencies(stereo-inertial rclcpp sensor_msgs cv_bridge ORB_SLAM3 Pangolin)
target_link_libraries(stereo-inertial ${OpenCV_LIBS})

# === 安装目标 ===
install(TARGETS mono rgbd stereo stereo-inertial
  DESTINATION lib/${PROJECT_NAME})

ament_package()
