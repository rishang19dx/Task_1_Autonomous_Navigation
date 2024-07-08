# Autonomous Navigation with TurtleBot3 in Gazebo

## Task Overview

This task involves using TurtleBot3 for autonomous navigation in a Gazebo simulation environment using ROS default planners. The steps include creating a map of the environment, traversing autonomously from point A to point B, and visualizing the robot's environment using RViz.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Setup](#setup)
3. [Mapping the Environment](#mapping-the-environment)
4. [Autonomous Navigation](#autonomous-navigation)
5. [Results](#results)
6. [References](#references)

## Prerequisites

Ensure you have the following software installed:

- ROS (Robot Operating System)
- Gazebo
- TurtleBot3 packages
- Git

## Setup

Clone the project repository:

```bash
git clone https://github.com/rishang19dx/Task_1_Autonomous_Navigation.git
cd Task_1_Autonomous_Navigation
```

### Install Required Packages

Make sure all dependencies are installed. If not, follow these steps:

1. **Install ROS and TurtleBot3 Packages:**

    ```bash
    sudo apt-get update
    sudo apt-get install ros-noetic-desktop-full
    sudo apt-get install ros-noetic-turtlebot3 ros-noetic-turtlebot3-simulations
    ```

2. **Set Up Workspace:**

    ```bash
    mkdir -p ~/ws/src
    cd ~/ws/
    catkin_make
    source devel/setup.bash
    ```

## Mapping the Environment

1. **Launch Gazebo World and TurtleBot3:**

    ```bash
    export TURTLEBOT3_MODEL=burger
    roslaunch turtlebot3_gazebo turtlebot3_world.launch
    ```

2. **Creating and saving the map:**
   - Mapping the environment manually with teleoperation proved to be a bulky task to me. Hence, I used a plugin which creates 2D occupancy map of the world at a given height
   - To include the plugin, add the following line in between the <world> </world> tags of your Gazebo world file:

     ```bash
     <plugin name='gazebo_occupancy_map' filename='libgazebo_2Dmap_plugin.so'>
         <map_resolution>0.1</map_resolution> <!-- in meters, optional, default 0.1 -->
         <map_height>0.3</map_height>         <!-- in meters, optional, default 0.3 -->
         <map_size_x>100</map_size_x>          <!-- in meters, optional, default 10 -->
         <map_size_y>100</map_size_y>          <!-- in meters, optional, default 10 -->
         <init_robot_x>0</init_robot_x>          <!-- x coordinate in meters, optional, default 0 -->
         <init_robot_y>0</init_robot_y>          <!-- y coordinate in meters, optional, default 0 -->
     </plugin>
     ```
    - To generate the map, call the `/gazebo_2Dmap_plugin/generate_map` ros service:
      
      ```bash
      rosservice call /gazebo_2Dmap_plugin/generate_map
      ```
    - The generated map is published on the `/map2d` ros topic.
    - You can use the map_saver node from the map_server package inside ros navigation to save your generated map to a .pgm and .yaml file:
       ```bash
       rosservice call /gazebo_2Dmap_plugin/generate_maprosrun map_server map_saver -f hospital /map:=/map2d
       ```
    - The last map generated with the `/gazebo_2Dmap_plugin/generate_map` call is saved.

## Autonomous Navigation

1. **Launch Navigation with the Saved Map:**

    ```bash
    export TURTLEBOT3_MODEL=burger
    roslaunch turtlebot3_navigation turtlebot3_navigation.launch map_file:=$HOME/hospital.yaml
    ```

2. **Set Initial Pose and Goal in RViz:**

    Use RViz to set the initial pose of the robot and the goal location for autonomous navigation.


## Results

- **Mapping:**
  - A complete map of the environment will be created .

- **Autonomous Navigation:**
  - The robot will autonomously navigate from point A to point B using the generated map and default planners.

## References

- [TurtleBot3 Documentation](https://emanual.robotis.com/docs/en/platform/turtlebot3/overview/)
- [ROS Navigation Stack](http://wiki.ros.org/navigation)
- [Gazebo Worlds Repository](https://github.com/mlherd/Dataset-of-Gazebo-Worlds-Models-and-Maps)
- [Gazebo_ros_2Dmap_plugin](https://github.com/marinaKollmitz/gazebo_ros_2Dmap_plugin)

---
