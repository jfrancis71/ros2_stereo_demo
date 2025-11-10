## Not ready for release


Python stereo split launch:
```
ros2 run stereo_demo stereo_split_node --ros-args --remap /image:=/server/image -p left_camera_info_url:=file:///root/ros2_ws/calration/left.yaml -p right_camera_info_url:=file:///root/ros2_ws/calibration/right.yaml
```

To calibrate:
```
ros2 run camera_calibration cameracalibrator --approximate 0.1 --size 8x6 --square 0.108 --ros-args -r right:=/right/image_raw -r left:=/left/image_raw
```

To launch stereo:
```
ros2 launch stereo_image_proc stereo_image_proc.launch.py
```

To view depth disparity map:
```
ros2 run image_view disparity_view --ros-args -r /image:=/disparity
```

View point cloud in rviz2, add topic /points2 and change global frame to camera_frame


Static transform to reorientate from camera frame coords to ROS2 coords:
```
ros2 run tf2_ros static_transform_publisher 0.0 0.0 0.0 -1.57 0.0 -1.57 base_link camera_frame
```

## References

https://docs.ros.org/en/jazzy/p/camera_calibration/doc/tutorial_stereo.html
