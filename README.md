# subo_rpi

Image for rpi https://downloads.ubiquityrobotics.com/pi.html

```sh
aakash@aakash:~$ ssh ubuntu@10.42.0.1
# password: ubuntu
# copying from machine a to b while logged in to b
aakash@aakash:~$ sudo scp -r ubuntu@10.42.0.1:~/catkin_ws ~/Downloads
```

## create package in rpi
```sh
ubuntu@ubiquityrobot:~$ printenv | grep ROS
ubuntu@ubiquityrobot:~$ mkdir -p ~/subo_rpi/src
ubuntu@ubiquityrobot:~$ cd subo_rpi/
ubuntu@ubiquityrobot:~/subo_rpi$ catkin_make
ubuntu@ubiquityrobot:~/subo_rpi$ source devel/setup.bash
ubuntu@ubiquityrobot:~/subo_rpi$ echo $ROS_PACKAGE_PATH
ubuntu@ubiquityrobot:~/subo_rpi$ cd src/
ubuntu@ubiquityrobot:~/subo_rpi/src$ catkin_create_pkg bot_cntrl std_msgs rospy roscpp
ubuntu@ubiquityrobot:~/subo_rpi/src$ cd ..
ubuntu@ubiquityrobot:~/subo_rpi$ catkin_make
ubuntu@ubiquityrobot:~/subo_rpi$ . ~/subo_rpi/devel/setup.bash
```

## Raspicam installation
Install the camera package: https://github.com/UbiquityRobotics/raspicam_node
Test the installation
```sh
ubuntu@ubiquityrobot:~$ roslaunch raspicam_node camerav2_1280x960.launch
```
Use `rqt_image_view` on a connected computer to view the published image.

Getting images using the script
```sh
ubuntu@ubiquityrobot:~/subo_rpi$ roslaunch raspicam_node camerav3_410x308_30fps.launch enable_raw:=true
aakash@aakash:~/subo_base$ rosrun motion_plan imager.py
```

