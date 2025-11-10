## Not ready for release

# ROS2 Stereo Camera Demonstration

## Stereo Split Node

The camera used in this demonstration provides the stereo image as a single image where the right camera image is appended to the left camera image.
The stereo split node splits this single image into two seperate images, publishing on left/image_raw and right/image_raw. Additionally it supports the set_camera_info service on /left_camera/set_camera_info and /right_camera/set_camera_info. It publishes camera calibration information on /left/camera_info and /right/camera_info.

You can set the URL location to store the camera calibration information, the default is: file://${ROS_HOME}/camera_info/${NAME}.yaml.
If you are using containerization, you will likely want to choose a location which persists.

To launch:
```
ros2 run stereo_demo stereo_split_node --ros-args --remap /image:=/server/image -p left_camera_info_url:=file:///root/ros2_ws/calibration/left.yaml -p right_camera_info_url:=file:///root/ros2_ws/calibration/right.yaml
```

### Subscribed Topics

- /image

### Published Topics

- /left/image_raw
- /right/image_raw
- /left/camera_info
- /right/camera_info

### Available Services

- /left_camera/set_camera_info
- /right_camera/set_camera_info

### Parameters

- left_camera_info_url: URL to persist the camera calibration information
- right_camera_info_url: ...

## Calibration

```
ros2 run camera_calibration cameracalibrator --approximate 0.1 --size 8x6 --square 0.108 --ros-args -r right:=/right/image_raw -r left:=/left/image_raw
```

## Launch Stereo Processing
```
ros2 launch stereo_image_proc stereo_image_proc.launch.py
```

## View Stereo Disparity Map
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

https://docs.ros.org/en/jazzy/p/stereo_image_proc/doc/index.html

https://docs.ros.org/en/jazzy/p/camera_calibration/doc/tutorial_stereo.html
