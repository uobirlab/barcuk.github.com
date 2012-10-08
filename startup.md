---
layout: default
title: Getting Started
---

Before you can work on BARC projects, you must get yourself up to speed with the tools we use. This can be a slow and frustrating process at times, so *please* **ask for help** whenever you need it. This is one of the reasons the club exists. The following sections describe the steps you need to work through.

## Install Ubuntu

We do most of our work on [Ubuntu](http://ubuntu.com), so you need to get yourself a working install of this. We currently use version 12.04. There are a number of options for you to get Ubuntu. These include:

 * Download and install [a VirtualBox virtual machine of Ubuntu 12.04 with ROS already installed](http://nootrix.com/2012/09/virtualizing-ros/). This is certainly the quickest way to get started, and it is the least stressful (as VirtualBox will run on any OS). 

 * If you are on Windows, you can [install using Wubi](http://www.ubuntu.com/download/desktop/windows-installer). This does not require disk partitioning (and the risks associated with it), but instead installs Ubuntu inside your windows disk.

 * Alternatively install Ubuntu on a separate (or single) partition on your machine. The [download page is here](http://www.ubuntu.com/download/desktop).

 If you have questions, a great place to start is [Ask Ubuntu](http://askubuntu.com).

If you are new Linux, or unfamiliar with using the command line, it would be good for you to work through a tutorial on this subject, e.g. [this one](http://www.ee.surrey.ac.uk/Teaching/Unix/).

## Install ROS

All our robots are controlled using ROS. Once you have Ubuntu installed you should therefore install ROS (unless you used the virtual machine above). We are currently using the ROS version `fuerte`, and [the install instructions are here](http://www.ros.org/wiki/fuerte/Installation/Ubuntu). 

If you plan to work in Java, then you must also [install rosjava_core](http://docs.rosjava.googlecode.com/hg/rosjava_core/html/installing.html).

Next you need to familiarise yourself with ROS. Before working on an active BARC project you **must** read [the ROS start guide](http://www.ros.org/wiki/ROS/StartGuide) **and** work through **all** of [the beginner level ROS tutorials](http://www.ros.org/wiki/ROS/Tutorials). For Java coders, the tutorials for writing ROS code in Java can be found [here](http://docs.rosjava.googlecode.com/hg/rosjava_core/html/getting_started.html) and [here](http://ros.org/wiki/rosjava_core/Tutorials/rosjava_tutorial_pubsub).

If you have problems, ask a BARC member for help (e.g. via [the Facebook group](https://www.facebook.com/groups/barcuk/) or in the lab), or try [ROS Answers](https://www.facebook.com/groups/barcuk/)).

## Join GitHub and our organisation

We host our code on GitHub, and therefore manage our code using the Git version control system. If Git is unfamiliar to you, read this [Git basics guide](http://git-scm.com/book/en/Getting-Started-Git-Basics). If you are new to GitHub, read [this getting started guide](https://help.github.com/articles/set-up-git). Once you are registered on, and familiar with, GitHub, ask to join [the BARC organisation on GitHub](https://github.com/barcuk). Once you are in the organisation, demonstrate your GitHub mastery by adding your name to the [members list](./members.html) by cloning [the website repo](https://github.com/barcuk/barcuk.github.com), editing the appropriate file, then pushing back the changes. Alternatively fork the repo, make the edit to your fork, then issue a pull request for the change.

## Code a Basic Behaviour

Once you have done the above you are ready to go. Before working on a real robot problem, you should test your knowledge of ROS etc. by implementing a basic robot control behaviour in simulation. For example, write a ROS node that drives a mobile around, turning it away from obstacles (like a [Braitenberg vehicle](http://en.wikipedia.org/wiki/Braitenberg_vehicle)).

You should do this using the [Stage mobile robot simulator](http://www.ros.org/wiki/stage). Instructions on how to run a simulated robot in Stage are [on the ROS wiki](http://www.ros.org/wiki/stage/Tutorials/SimulatingOneRobot) (you can skip the telop parts if you want). To read the laser you should subscribe to the `base_scan` topic, which will contain messages in the form of `sensor_msgs::LaserScan`. The control the robot you should publish `geometry_msg::Twist` messages to the `cmd_vel` topic. For reference, to drive the robot forward at 1 m/s, set the x element of the `linear` vector of the twist to 1. The `cmd_vel` messages should be published repeatedly to keep the robot moving (if you stop publishing messages then the robot will stop moving). For Java coders, this is explored further [on our wiki](https://github.com/barcuk/barcuk.github.com/wiki/Controlling-a-Simulated-Robot-in-Java). For all other languages there should be enough examples in the ROS codebase itself.





