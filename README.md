# Programming a Real Self-Driving Car

## Introduction
This is the project repo for the final project of the Udacity Self-Driving Car Nanodegree: Programming a Real Self-Driving Car. For this project, I was writing ROS nodes to implement core functionality of the autonomous vehicle system, including traffic light detection, control, and waypoint following. In the end I tested my code using a simulator provided by Udacity. Finally, I submitted the project to be run on a real autonomous vehicle.

[//]: # (Image References)

[image1]: ./examples/System_Architecture_Diagram.png "System_Architecture_Diagram"
[image2]: ./examples/Traffic_Light_Detection_Node.png "STraffic_Light_Detection_Node"


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

### ./ros/src/tl_detector/
This package contains the traffic light detection node: `tl_detector.py`. This node takes in data from the `/image_color`, ``/current_pose`, and `/base_waypoints` topics and publishes the locations to stop for red traffic lights to the `/traffic_waypoint` topic.

The `/current_pose` topic provides the vehicle's current position, and `/base_waypoints` provides a complete list of waypoints the car will be following.

I built both a traffic light detection node and a traffic light classification node. Traffic light detection is takeing place within `tl_detector.py`, whereas traffic light classification is takeing place within `./tl_detector/light_classification_model/tl_classfier.py`.

![alt text][image2]