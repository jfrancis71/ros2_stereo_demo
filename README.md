## Not ready for release



```
ros2 run camera_calibration cameracalibrator --approximate 0.1 --size 8x6 --square 0.108 right:=/stereo/right/image_raw left:=/stereo/left/image_raw right_camera/set_camera_info:=/stereo/right/set_camera_info --ros-args -r left_camera/set_camera_info:=/stereo/left/set_camera_info
```

## References

https://docs.ros.org/en/jazzy/p/camera_calibration/doc/tutorial_stereo.html
