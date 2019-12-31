# subo_rpi
Beginner's tutorial for setting up ROS on RPi, communicating with the base PC and publishing the images from the RPi camera. 
The tutorial consists of two parts i.e. RPi workspace and base PC workspace (to be followed in sequence):
1. <https://github.com/nimRobotics/subo_rpi>
2. <https://github.com/nimRobotics/subo_base>

## Setting up RPi
Flash the RPi microSD card with the image provided by Ubiquity Robotics <https://downloads.ubiquityrobotics.com/pi.html>.
Once the RPi boots up it will create a WiFi access point named `ubiquityrobotXXXX`. Connect your PC with the access point. 

```sh
aakash@aakash:~$ ssh ubuntu@10.42.0.1
# password: ubuntu

# In case you are not using this RPi image for running Ubiquity Robotics robots, diasble their running scripts
ubuntu@ubiquityrobot:~$ sudo systemctl disable magni-base
ubuntu@ubiquityrobot:~$ sudo reboot

# To copy a file from machine a to b while logged in to b
aakash@aakash:~$ sudo scp -r ubuntu@10.42.0.1:~/dir/to/copy/from ~/dir/to/copy/to 
```

## Creating workspace and package in RPi
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

## Setting up RPi(master) ROS communication with base PC
```sh
ubuntu@ubiquityrobot:~$ hostname -I
10.42.0.1
ubuntu@ubiquityrobot:~$ export ROS_IP=10.42.0.1
#check the ROS environment variables i.e. ROS_IP
ubuntu@ubiquityrobot:~$ printenv | grep ROS

aakash@aakash:~$ hostname -I
10.42.0.81
aakash@aakash:~$ export ROS_MASTER_URI=http://ubiquityrobot.local:11311
aakash@aakash:~$ export ROS_IP=10.42.0.1

# publish from RPi
ubuntu@ubiquityrobot:~$ rostopic pub /test2/topic std_msgs/String 'Hello World from RPi' -r 1
# test in base PC
aakash@aakash:~$ rostopic echo /test/topic
# publish from base PC
aakash@aakash:~$ rostopic pub /test2/topic std_msgs/String 'Hello World from base PC' -r 1
# test in RPi
ubuntu@ubiquityrobot:~$ rostopic echo /test/topic
```

## Raspicam installation
Install the camera package: https://github.com/UbiquityRobotics/raspicam_node
Test the installation `ubuntu@ubiquityrobot:~$ roslaunch raspicam_node camerav2_1280x960.launch`. Use `rqt_image_view` on a connected computer to view the published image. To create a custom launch file for the camera, populate the `ubuntu@ubiquityrobot:~$ cd subo_rpi/src/raspicam_node/launch/camerav3_410x308_30fps.launch` launch file with the below script:

```xml
<launch>
  <arg name="enable_raw" default="false"/>
  <arg name="enable_imv" default="false"/>
  <arg name="camera_id" default="0"/>
  <arg name="camera_frame_id" default="raspicam"/>
  <arg name="camera_name" default="camerav2_410x308"/>

  <node type="raspicam_node" pkg="raspicam_node" name="raspicam_node" output="screen">
    <param name="private_topics" value="true"/>

    <param name="camera_frame_id" value="$(arg camera_frame_id)"/>
    <param name="enable_raw" value="$(arg enable_raw)"/>
    <param name="enable_imv" value="$(arg enable_imv)"/>
    <param name="camera_id" value="$(arg camera_id)"/>

    <param name="camera_info_url" value="package://raspicam_node/camera_info/camerav2_410x308.yaml"/>
    <param name="camera_name" value="$(arg camera_name)"/>
    <param name="width" value="256"/>
    <param name="height" value="192"/>

    <param name="framerate" value="30"/>
    <param name="exposure_mode" value="antishake"/>
    <param name="shutter_speed" value="0"/>
  </node>
</launch>
```

Getting images on base PC using the python script <https://github.com/nimRobotics/subo_base>
```sh
ubuntu@ubiquityrobot:~/subo_rpi$ roslaunch raspicam_node camerav3_410x308_30fps.launch enable_raw:=true
aakash@aakash:~/subo_base$ rosrun motion_plan imager.py
```
