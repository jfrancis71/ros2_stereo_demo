## Not ready for release

# ROS2 Stereo Camera Demonstration

## Overview

In this demonstration I show how to setup a stereo camera so it is publishing the correct topics. I show how to calibrate the camera. I then show how to view the disparity map. Then I show how to view the PointCloud in rviz2 and finally, for those of you using NAV2 mapping, how to place this PointCloud correctly in your map (in rviz2).

## Stereo Split Node

This node is only needed when your camera provides the stereo image as a single image with the two camera images side by side. This node will split that single image down the middle with the left side and right side published on seperate topics (see published topics below). This node also supports publishing on the CameraInfo topic. If your camera driver already does this, you will not need this node.

To launch:
```
ros2 run stereo_demo stereo_split_node --ros-args \
    --remap /image:=/server/image \
    -p left_camera_info_url:=file:///root/ros2_ws/calibration/left.yaml \
    -p right_camera_info_url:=file:///root/ros2_ws/calibration/right.yaml
```

In the above line I am setting the url's appropriately for my ROS2 install (I am using Docker). You will likely wish to use a location which persists to save you from needing to recalibrate.

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

In below you should check the size and square parameters, please see the camera calibration reference at bottom. Please pay extra attention to the size parameter in the documentation (it is a little confusing).

```
ros2 run camera_calibration cameracalibrator --approximate 0.1 --size 8x6 --square 0.0177 \
    --ros-args -r left:=/left/image_raw -r right:=/right/image_raw
```

It is worth a little patience with this step as you will get much better results.

## Launch Stereo Processing
```
ros2 launch stereo_image_proc stereo_image_proc.launch.py sgbm_mode:=2
```

## View Stereo Disparity Map
To view depth disparity map:
```
ros2 run image_view disparity_view --ros-args -r /image:=/disparity
```

## View PointCloud in rviz2

In rviz2, add topic /points2.
Change global frame to camera_frame (in rviz2).

To reorientate from camera frame coords to ROS2 coords you can run the following:
```
ros2 run tf2_ros static_transform_publisher 0.0 0.0 0.0 -1.57 0.0 -1.57 base_link camera_frame
```
Change global frame to base_link (again in rviz2).

Finally if you wish to see this superimposed on your own map (using Nav2) bringup your Nav2 stack, and change your global frame to /map.
The PointCloud should now be showing superimposed on your map.

## References

https://docs.ros.org/en/jazzy/p/stereo_image_proc/doc/index.html

https://docs.ros.org/en/jazzy/p/camera_calibration/doc/tutorial_stereo.html
