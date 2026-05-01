
[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/7KaPTW5f)
# The Odyssey

# Coffee Transportation Solution
<img width="761" height="610" alt="Screenshot 2026-05-01 111158" src="https://github.com/user-attachments/assets/81ff2742-61a4-4094-a57f-a1af5425e497" />
<img width="981" height="492" alt="Screenshot 2026-05-01 111132" src="https://github.com/user-attachments/assets/cb86e10f-fba3-43ac-a552-a15600267145" />
# Mechanical Design

# Hardware Installation Guide
The hardware installation was not the most high quality job but as far as immediate necessity, it works efficiently and in a pinch
<img width="1156" height="868" alt="1144" src="https://github.com/user-attachments/assets/b5432ba8-c487-4c71-b183-c3ac8ce2e0ce" />
<img width="868" height="1156" alt="1143" src="https://github.com/user-attachments/assets/45ae8755-7473-4fdf-b3e3-159fcd6213ac" />




# Software Installation Guide
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

# Software Usage Instructions

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

### Navigation
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
