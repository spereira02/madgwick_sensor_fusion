To build package
```bash
colcon build --symlink-install
source install/setup.bash
ros2 launch imu_madgwick imu_filter.launch.py
```
