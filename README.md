# ORB_SLAM3_ROS2_FIXED

A fixed and improved ROS2 wrapper for **ORB-SLAM3**, based on the original repository by [zang09](https://github.com/zang09/ORB_SLAM3_ROS2).
 This version **resolves several common build/runtime issues** and **enhances compatibility** with modern ROS2 and OpenCV environments.

------

## ✅ What's Fixed

### 问题 1：OpenCV 共享对象文件冲突

**错误表现：**
 编译或运行时出现 OpenCV 版本不一致导致的符号冲突。

**解决方法：**
 在 `CMakeLists.txt` 中**明确指定** OpenCV 版本路径：

```cmake
# === 指定 OpenCV 4.4.0 ===
set(OpenCV_DIR "/usr/local/lib/cmake/opencv4")  # 按你的 OpenCV 安装路径修改
find_package(OpenCV REQUIRED)
```

------

### 问题 2：缺失 OpenCV 模块

**错误表现：**
 链接报错，提示找不到如 `opencv_calib3d` 等模块。

**解决方法：**
 在 `CMakeLists.txt` 中手动添加所需模块：

```cmake
# 手动追加需要的 OpenCV 模块
list(APPEND OpenCV_LIBS opencv_calib3d opencv_core opencv_imgproc opencv_highgui)
```

------

### 问题 3：运行时崩溃 `double free or corruption (out)`

**错误表现：**
 运行时出现如下错误并中止：

```
double free or corruption (out)
[ros2run]: Aborted
```

**原因分析：**
 源码中误用了 `shared_ptr<rclcpp::Node>(this)`，导致 `this` 被多个 `shared_ptr` 管理，造成重复释放。

**修复方法：**
 在 `rgbd-slam-node.cpp` 的构造函数中修改如下：

```cpp
// 修改前
rgb_sub = std::make_shared<message_filters::Subscriber<ImageMsg>>(
    std::shared_ptr<rclcpp::Node>(this),
    "/camera/camera/color/image_raw");
depth_sub = std::make_shared<message_filters::Subscriber<ImageMsg>>(
    std::shared_ptr<rclcpp::Node>(this),
    "/camera/camera/aligned_depth_to_color/image_raw");

// 修改后
rgb_sub = std::make_shared<message_filters::Subscriber<ImageMsg>>(
    this, "camera/camera/color/image_raw");
depth_sub = std::make_shared<message_filters::Subscriber<ImageMsg>>(
    this, "camera/camera/aligned_depth_to_color/image_raw");
```

**修改原因说明：**
 原写法中创建了新的 `shared_ptr` 管理已有对象，导致多次释放。
 修改后传递裸指针 `this` 是 ROS2 推荐的做法，**避免了内存重复释放的问题**。

------

## 原始项目来源

本仓库基于开源项目 [zang09/ORB_SLAM3_ROS2](https://github.com/zang09/ORB_SLAM3_ROS2) 修改而来，在此基础上进行了错误修复与兼容性改进。