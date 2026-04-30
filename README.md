[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/7KaPTW5f)
# The Odyssey

## Coffee Transportation Solution

### Mechanical Design

### Hardware Installation Guide

## Install homer_bringup Package

```
mkdir -p ~/homer_ws/src  # create a workspace
cd ~/homer_ws/src  # navigate into `src/` under the workspace
git clone https://github.com/linzhanguca/homer_bringup.git  # download package
cd ~/homer_ws  # navigate to workspace root
rosdep install -y --from-paths src --ignore-src --rosdistro $ROS_DISTRO  # let `rosdep` install missing dependencies
colcon build  # build all packages in workspace
source ~/homer_ws/install/local_setup.bash  # register all resources in the current workspace
echo "source ~/homer_ws/install/local_setup.bash" >> ~/.bashrc  # auto register
```
## Install homer_navigation Package
```
mkdir -p ~/homer_ws/src  # create a workspace
cd ~/homer_ws/src  # navigate into src/ under the workspace
git clone https://github.com/linzhanguca/homer_navigation.git  # download package
cd ~/homer_ws  # navigate to workspace root
colcon build  # build all packages in workspace
echo "source ~/homer_ws/install/local_setup.bash" >> ~/.bashrc  # register package related resources
source ~/.bashrc
```

## Software Usage Instructions

### Create a Map
Set HomeR at a good starting location on the ground.

Open a terminal on RPi, launch homer_interface and related resources
```
ros2 launch homer_bringup homer.launch.py
```
Open a terminal on Server, launch slam_toolbox under the "mapping" mode.
```
ros2 launch homer_navigation create_map.launch.py
```
(A Rviz window will be popped out.)

Open a terminal either on Server or RPi, start teleop_twist_keyboard to drive the robot around and create the map simultaneously.
```
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

### Save the Map
In Rviz, go to "SlamToolboxPlugin" panel, and locate the "Serialize Map" button.
Type the absolute path for the map which is intended to be saved in the box next to the "Serialize Map" button.

(Use absolute path. For example, /tmp/test_map. The shortcut, ~/, for the home directory will not work. You will need to explicitly express the home directory, for example: /home/<username>/<follow_up_paths>.)

Click "Serialize Map" button, then verify if map files are saved successfully.

# Navigation
Open localization configuration file $HOME/homer_ws/src/homer_navigation/configs/localization_params.yaml, and edit map_file_name around line 18. Use the map name you've saved in the previous steps, for example:

map_file_name: /tmp/test_map
Rebuild the navigation package with the specified map files.
```
cd ~/homer_ws/
colcon build
source install/local_setup.bash
```

On RPi, launch homer_interface and rplidar_composition
```
ros2 launch homer_bringup homer.launch.py
```
On Server, launch slam_toolbox under the "localization" mode and a collection of nav2 nodes.

```
ros2 launch homer_navigation navigation.launch.py
```
(Optional) If the lidar scan and the map are misaligned, use "2D Pose Estimate" button on the top of the Rviz to set initial pose for HomeR.

In Rviz, use "2D Goal Pose" button to set desired ending pose for HomeR.

(Nav2 parameters have been modified to improve navigation.)
