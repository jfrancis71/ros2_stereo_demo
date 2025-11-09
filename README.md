## Not ready for release


Python stereo split launch:
```
python3 src/ros2_lego/repub/stereo_split.py --ros-args -r /image:=/server/image -p left_camera_info_url:=file:///root/ros2_ws/cal/left.yaml -p right_camera_info_url:=file:///root/ros2_ws/cal/right.yaml
```

To calibrate:
```
ros2 run camera_calibration cameracalibrator --approximate 0.1 --size 8x6 --square 0.108 right:=/stereo/right/image_raw left:=/stereo/left/image_raw right_camera/set_camera_info:=/stereo/right/set_camera_info --ros-args -r left_camera/set_camera_info:=/stereo/left/set_camera_info
```

To launch stereo:
```
ros2 launch stereo_image_proc stereo_image_proc.launch.py namespace:=stereo
```

To view depth disparity map:
```
ros2 run image_view disparity_view --ros-args --remap /image:=/stereo/disparity
```

View point cloud in rviz2, add topic /stereo/points2 and change global frame to camera_frame

## References

https://docs.ros.org/en/jazzy/p/camera_calibration/doc/tutorial_stereo.html
