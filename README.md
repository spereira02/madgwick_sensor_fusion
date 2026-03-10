## Overview

This repository implements the **ROS 2 side of an end-to-end IMU processing pipeline**.

Sensor data produced by an ESP32-based embedded system is streamed into ROS 2 via a **micro-ROS agent**.  
Once inside the ROS ecosystem, the data is processed using the **Madgwick orientation filter** to estimate the 3D orientation of the IMU.

This repository provides:

- launch configuration for the **micro-ROS agent**
- integration with the **imu_filter_madgwick** package
- configuration of the sensor fusion parameters
- RViz visualization of the resulting orientation estimate

The goal is to demonstrate how low-level embedded sensor data can be integrated into a distributed robotics system using ROS 2.

This repository therefore represents **Step 2 of the IMU processing pipeline**.

➡️ The full system architecture — including firmware, ROS 2 integration, and hardware setup — is documented in the main project repository:  
**[https://github.com/spereira02/Fullstack_imu_filter]**
To build package
```bash
colcon build --symlink-install
source install/setup.bash
ros2 launch imu_madgwick imu_filter.launch.py
```
