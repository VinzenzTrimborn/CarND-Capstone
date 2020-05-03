# Programming a Real Self-Driving Car

## Introduction
This is the project repo for the final project of the Udacity Self-Driving Car Nanodegree: Programming a Real Self-Driving Car. For this project, I was writing ROS nodes to implement core functionality of the autonomous vehicle system, including traffic light detection, control, and waypoint following. In the end I tested my code using a simulator provided by Udacity. Finally, I submitted the project to be run on a real autonomous vehicle.

[//]: # (Image References)

[image1]: ./examples/System_Architecture_Diagram.png "System_Architecture_Diagram"
[image2]: ./examples/tl-detector-ros-graph.png "tl-detector-ros-graph"
[image3]: ./examples/waypoint-updater-ros-graph.png "waypoint-updater-ros-graph"
[image4]: ./examples/dbw-node-ros-graph.png "dbw-node-ros-graph"


## Native Installation

* Be sure that your workstation is running Ubuntu 16.04 Xenial Xerus or Ubuntu 14.04 Trusty Tahir. [Ubuntu downloads can be found here](https://www.ubuntu.com/download/desktop).
* If using a Virtual Machine to install Ubuntu, use the following configuration as minimum:
  * 2 CPU
  * 2 GB system memory
  * 25 GB of free hard drive space

  The Udacity provided virtual machine has ROS and Dataspeed DBW already installed, so you can skip the next two steps if you are using this.

* Follow these instructions to install ROS
  * [ROS Kinetic](http://wiki.ros.org/kinetic/Installation/Ubuntu) if you have Ubuntu 16.04.
  * [ROS Indigo](http://wiki.ros.org/indigo/Installation/Ubuntu) if you have Ubuntu 14.04.
* [Dataspeed DBW](https://bitbucket.org/DataspeedInc/dbw_mkz_ros)
  * Use this option to install the SDK on a workstation that already has ROS installed: [One Line SDK Install (binary)](https://bitbucket.org/DataspeedInc/dbw_mkz_ros/src/81e63fcc335d7b64139d7482017d6a97b405e250/ROS_SETUP.md?fileviewer=file-view-default)
* Download the [Udacity Simulator](https://github.com/udacity/CarND-Capstone/releases).

### Usage

1. Clone the project repository
```bash
git clone https://github.com/udacity/CarND-Capstone.git
```

2. Install python dependencies
```bash
cd CarND-Capstone
pip install -r requirements.txt
```
3. Make and run styx
```bash
cd ros
catkin_make
source devel/setup.sh
roslaunch launch/styx.launch
```
4. Run the simulator

## System Architecture

The following is a system architecture diagram showing the ROS nodes and topics used in the project.

![alt text][image1]

### Code Structure

Below is a brief overview of the repo structure, along with descriptions of the ROS nodes. The code that I modified for the project is contained entirely within the ./ros/src/ directory. Within this directory, you will find the following ROS packages:

#### ./ros/src/tl_detector/
This package contains the traffic light detection node: `tl_detector.py`. This node takes in data from the `/image_color`, `/current_pose`, and `/base_waypoints` topics and publishes the locations to stop for red traffic lights to the `/traffic_waypoint` topic.

The `/current_pose` topic provides the vehicle's current position, and `/base_waypoints` provides a complete list of waypoints the car will be following.

I built both a traffic light detection node and a traffic light classification node. Traffic light detection is takeing place within `tl_detector.py`, whereas traffic light classification is takeing place within `./tl_detector/light_classification_model/tl_classfier.py`.

![alt text][image2]

#### ./ros/src/waypoint_updater/
This package contains the waypoint updater node: `waypoint_updater.py`. The purpose of this node is to update the target velocity property of each waypoint based on traffic light and obstacle detection data. This node will subscribe to the `/base_waypoints`, `/current_pose`, `/obstacle_waypoint`, and `/traffic_waypoint`topics, and publish a list of waypoints ahead of the car with target velocities to the `/final_waypoints` topic.

![alt text][image3]

#### ./ros/src/twist_controller/
The autonomous car, Carla is equipped with a drive-by-wire (dbw) system, meaning the throttle, brake, and steering have electronic control. This package contains the files that are responsible for control of the vehicle: the node `dbw_node.py` and the file `twist_controller.py`, along with a pid and lowpass filter that we used in your implementation. The `dbw_node` subscribes to the `/current_velocity` topic along with the `/twist_cmd` topic to receive target linear and angular velocities. Additionally, this node will subscribe to `/vehicle/dbw_enabled`, which indicates if the car is under dbw or driver control. This node will publish throttle, brake, and steering commands to the `/vehicle/throttle_cmd`, `/vehicle/brake_cmd`, and `/vehicle/steering_cmd` topics.

![alt text][image4]

In addition to these packages you will find the following, which I did not change for the project. The `styx` and `styx_msgs` packages are used to provide a link between the simulator and ROS, and to provide custom ROS message types:

* ./ros/src/styx/
A package that contains a server for communicating with the simulator, and a bridge to translate and publish simulator messages to ROS topics.

* ./ros/src/styx_msgs/
A package which includes definitions of the custom ROS message types used in the project.

* ./ros/src/waypoint_loader/
A package which loads the static waypoint data and publishes to `/base_waypoints`.

* ./ros/src/waypoint_follower/
A package containing code from Autoware which subscribes to `/final_waypoints` and publishes target vehicle linear and angular velocities in the form of twist commands to the `/twist_cmd` topic.

## Order of Project Development
Because I wrote code across several packages with some nodes depending on messages published by other nodes, I completed the project in the following order:


1. Waypoint Updater Node (Partial): Completed a partial waypoint updater which subscribes to `/base_waypoints` and `/current_pose` and publishes to `/final_waypoints`.
2. DBW Node: Once my waypoint updater is publishing `/final_waypoints`, the waypoint_follower node will start publishing messages to the`/twist_cmd` topic. With this information I was ready to build the dbw_node. After this step was compelted, the car was driveing in the simulator, ignoring the traffic lights.
3. Traffic Light Detection: This was split into 2 parts:
* Detection: Detect the traffic light and its color from the `/image_color`. 
+ Waypoint publishing: Convert it to a waypoint index and publish it.
4. Waypoint Updater (Full): I used `/traffic_waypoint` to change the waypoint target velocities before publishing to `/final_waypoints`. My car is now stoping at red traffic lights and moveing when they are green.