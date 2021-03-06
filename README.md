# rviz_waypoint_navigator
GUI interface for waypoint_navigator using Rviz.

## Installation

1. Install ROS kinetic (if you haven't done so):

```bash
$ sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu `lsb_release -sc` main" > /etc/apt/sources.list.d/ros-latest.list'
$ wget http://packages.ros.org/ros.key -O - | sudo apt-key add -
$ sudo apt-get update
$ sudo apt-get install ros-kinetic-desktop-full 
$ sudo rosdep init
$ rosdep update
$ source /opt/ros/kinetic/setup.bash
```

2. extra ROS packages, catkin-tools, wstool, and others:
```
$ sudo apt-get install ros-kinetic-joy ros-kinetic-octomap-ros ros-kinetic-mavlink ros-kinetic-control-toolbox ros-kinetic-cmake-modules ros-kinetic-mavros ros-kinetic-gazebo-plugins ros-kinetic-gazebo-ros ros-kinetic-xacro 
$ sudo apt-get install python-wstool python-catkin-tools autoconf protobuf-compiler libgoogle-glog-dev liblapacke-dev
$ sudo rosdep init
$ rosdep update
```

3. Recommended to setup a clean catkin workspace

```
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws
catkin init
catkin config --extend /opt/ros/kinetic
catkin config --cmake-args -DCMAKE_BUILD_TYPE=Release
catkin config --merge-devel
```

4. We use wstool to install dependencies and keep track of versions.
```
cd src
wstool init
wstool set --git rviz_waypoint_navigator git@github.com:ToniRV/rviz_waypoint_navigator.git -y
wstool update
wstool merge rviz_waypoint_navigator/install/gazebo_rotors_simulation.rosinstall
wstool update -j8
```

5. Source your newly created workspace
```
source ~/catkin_ws/devel/setup.bash
```

Alternatively, add this command to your bashrc to avoid having to source your workspace for each new terminal.
```
echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

6. Finally, build the workspace:
```
catkin build
```

6.1. If you have compilation issues with `rotors_hil_interface`, just do not compile it, as we are not using it:
```
roscd rotors_hil_interface && touch CATKIN_IGNORE
```

7. Finally, install gazebo (version >= 8.0)
```
curl -sSL http://get.gazebosim.org | sh
```

## Usage with Gazebo and RVIZ

1. In a new terminal (remember to source the workspace), type:

 ```
 $ roslaunch waypoint_navigator mav_sim.launch
 ```
 
3. In a new command window, type:

 ```
 $ roslaunch rviz_waypoint_navigator rviz_waypoint_navigator.launch
 ```

4. Launch RVIZ using the configuration in the ``rviz`` folder:
```
rviz -d ~/catkin_ws/src/rviz_waypoint_navigator/rviz/rviz_waypoint_navigator.rviz
```

> **NOTE** If you do not see the drawn drone in Rviz, reset the InteractiveMarkers topic at the left side panel in Rviz by clicking on ``Update Topic`` and clicking to the topic which should be ``/firefly/rviz_waypoint_navigator/update``.
Repeat several times, until you see the drone with interactive markers, otherwise try to relaunch the launch files.

Now you should see two screens:
a) Gazebo with the drone
b) RVIZ with a drawn drone with interactive markers shaped as a frame of reference.
You can click and drag along these arrows to move the drawn drone around.
Then, right click on it: a drop-menu should appear.
Click on ``Store pose as waypoint``.
Repeat as long as you want.
Then, right click again to see the menu and click ``Plan path through stored waypoints``.
You should see the drone moving.

See Issues section below if you encounter problems.

## Simulation
  ### Gazebo + RotorS

## Issues
### Compilation Issues
- If eigen_checks does not compile because it cannot find gtest, it appears that one potential solution would be to delete the file in ``sudo mv /usr/share/cmake-3.5/Modules/FindGMock.cmake /usr/share/cmake-3.5/Modules/FindGMock.cmake.backup``.
As explained here: https://stackoverflow.com/questions/51969824/catkin-cannot-find-gtest
