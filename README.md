## Not ready for release

# ROS2 Stereo Camera Demonstration

## Overview

In this demonstration I show how to:

- Setup a stereo camera so it is publishing the correct topics
- Calibrate the camera
- View the disparity map
- View the PointCloud in rviz2
- View the PointCloud in rviz2 orientated within a NAV2 map (this is for those of you who are running NAV2)

Out of scope: Setting up your robot, joystick controller, NAV2 stack. I assume you already have these setup.

## Test Environment

Tested on ROS2 Jazzy on the Desktop.
Robot is a Raspberry Pi 3B+ (lego robot), running Ubuntu 22.04 with ROS2 Jazzy.

The robot stereo camera is "ELP-USB3D1080P02-H120-DE 1080P 60FPS USB Webcam Dual 120 degree No Distortion Lens Synchronization Stereo Camera Module UVC USB 2.0"

## Prerequisite Packages

You should use the appropriate procedure for whatever is your preferred ROS2 setup. I don't cover that here as everyone has a different preferred install, eg. system-wide, build from source, docker etc...

You will need to have installed the following packages:

ros-jazzy-camera-calibration

ros-jazzy-stereo-image-proc

ros-jazzy-image-view

## Stereo Split Node

This node is only needed when your camera provides the stereo image as a single image with the two camera images side by side. This node will split that single image down the middle with the left side and right side published on seperate topics (see published topics below). This node also supports publishing on the CameraInfo topic. If your camera driver already does this, you will not need this node.

### Install

```
git -C src clone https://github.com/jfrancis71/ros2_stereo_demo
colcon build --symlink-install --packages-select=stereo_demo
source ./install/setup.bash
```

This package also uses the ROS2 Python CameraInfoManager package (this manages the SetCameraInfo service). Unfortunately the implementation on the official Jazzy looks incorrect. For the moment I suggest use the following repository:

```
git -C src clone https://github.com/furbrain/image_common/tree/rolling
colcon build --symlink-install --packages-select=camera_info_manager_py
source ./install/setup.bash
```

### Launch

```
ros2 run stereo_demo stereo_split_node --ros-args \
    --remap /image:=/server/image \
    -p left_camera_info_url:=file:///root/ros2_config/calibration/left.yaml \
    -p right_camera_info_url:=file:///root/ros2_config/calibration/right.yaml
```

In the above line I am setting the url's appropriately for my ROS2 install (I am using Docker). You will likely wish to use a location which persists to save you from needing to recalibrate. My ros2_config folder above is a persistent docker volume.

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

In below you should check the size and square parameters, please see the camera calibration reference in the References section. Please pay extra attention to the size parameter in the documentation (it is a little confusing).

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
