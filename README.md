Part of the Fullstack IMU pipeline project: [**https://github.com/spereira02/Fullstack_imu_filter**]
# madgwick_sensor_fusion

## Overview

This repository implements the **ROS 2 side of an IMU processing pipeline**.

Raw IMU measurements are produced by an **ESP32 micro-ROS client** (see the firmware repository) and published to the ROS 2 graph via the **micro-ROS Agent**.

This package launches the **Madgwick orientation filter**, which estimates IMU orientation from accelerometer, gyroscope, and magnetometer data. The resulting orientation is visualized in **RViz**.

This repository therefore represents **Step 2 of the IMU processing pipeline**.

The full system — including ROS 2 integration, sensor fusion using the **Madgwick filter**, and RViz visualization — is documented in the main project repository:  
**[https://github.com/spereira02/Fullstack_imu_filter]**

Firmware repository:  
https://github.com/spereira02/esp32_firmware

---

## System Architecture

<p align="center">
  <img src="docs/architecture.png" width="850"/>
</p>

<p align="center">
End-to-end IMU data pipeline: an ESP32 reads an ICM-20948 sensor via I²C, publishes measurements through a micro-ROS client over serial (XRCE-DDS), and a ROS 2 system performs orientation estimation using the Madgwick filter and visualizes the result in RViz.
</p>

---

## Repository Structure

```
madgwick_sensor_fusion/
├── config/
│   └── imu_filter.yaml
│
├── launch/
│   └── madgwick_launch.py
│
├── package.xml
├── setup.py
└── setup.cfg
```

---

## Components

### Launch File

```
launch/madgwick_launch.py
```

The launch file starts **three ROS 2 processes simultaneously**:

1. **micro-ROS Agent**  
   Creates the serial bridge between the ESP32 and the ROS 2 graph.

2. **Madgwick Filter Node (`imu_filter_madgwick`)**  
   Consumes `/imu/data_raw` and `/imu/mag` topics and estimates the IMU orientation.

3. **RViz**  
   Visualizes the estimated orientation and TF frames.

Launching everything together simplifies bring-up of the full pipeline.

---

### Filter Configuration

```
config/imu_filter.yaml
```

Contains parameters for the **Madgwick filter**, such as:

- magnetometer usage
- filter gain
- TF publishing
- frame names

This allows filter tuning without modifying code.

---

### ROS Package Metadata

```
package.xml
setup.py
```

Defines the ROS 2 package and installs the launch and configuration files so they can be discovered by ROS.

This repository **does not implement the Madgwick filter itself** — it only launches and configures it.

---

## Required Dependencies

Two external packages must be present in the ROS workspace.

### Madgwick Filter

Install the ROS package containing the filter:

```bash
sudo apt install ros-jazzy-imu-tools
```

This provides:

```
imu_filter_madgwick
```

---

### micro-ROS Agent

Clone the micro-ROS agent into the same workspace.

Example workspace structure:

```
ros2_ws/
└── src/
    ├── madgwick_sensor_fusion
    └── micro-ROS-Agent
```

Clone it with:

```bash
cd ros2_ws/src
git clone -b jazzy https://github.com/micro-ROS/micro-ROS-Agent.git
```

Then build the workspace:

```bash
cd ..
rosdep install --from-paths src --ignore-src -r -y
colcon build
```

---

## Build

Clone this repository into a ROS workspace:

```bash
mkdir -p ros2_ws/src
cd ros2_ws/src

git clone https://github.com/spereira02/madgwick_sensor_fusion.git
```

Build the workspace:

```bash
cd ..
colcon build
source install/setup.bash
```

---

## Launch

Start the full ROS 2 pipeline:

```bash
ros2 launch imu_madgwick madgwick_launch.py
```

This launches:

- micro-ROS Agent  
- Madgwick filter node  
- RViz

---

## What This Repository Demonstrates

- launching multiple ROS 2 components from a single launch file
- configuring algorithms through YAML parameter files
- integrating embedded micro-ROS devices into a ROS 2 pipeline
- visualizing orientation estimation in RViz
