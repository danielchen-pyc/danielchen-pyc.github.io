---
title: ":potato: Potato Robotics"
author: daniel
layout: post
date: 2020-08-23 22:10
tag: 
- C++
- Circuit Design
- CAD
- Robotics
image: /assets/images/project/potato-robotics/FourBarLinkageGripper.gif
headerImage: true
projects: true
description: "Course Project for ENPH253: Fully Autonomous Robot from Scratch"
category: project
externalLink: false
source: PotatoRobotics_Alpha
---

# Project Goal
The ultimate goal for this course is to build a **fully autonomous** aluminum can collector that will be able to compete in the 253/480 Robot Competition. There are some key features that the robot must be able to perform:
- Line following
- Can detection
- Can collection
- Basic self-localization
- Return to starting position

The robot also needs to be **constructed entirely from scratch**, from prototyping, mechanical design, soldering, to software programming. 

# Brainstorming Ideas

Our team had multiple brainstorming sessions where we sketched some preliminary designs according to the required features above. 

<div class="side-by-side-normal">
    <div class="toleft">
        <img class="image" src="/assets/images/project/potato-robotics/c-sketch.jpeg">
    </div>
    <div class="toright">
        <img class="image" src="/assets/images/project/potato-robotics/c-sketch-2.jpeg">
    </div>
</div>
<figcaption class="side-by-side-caption">My Initial Design</figcaption>

<div class="wrapper-medium">
    <img class="image" src="/assets/images/project/potato-robotics/sayemdesign.png"/>
    <figcaption class="caption">Other Ideas</figcaption>
</div>

<br/>

<div class="wrapper-normal">
    <img class="image" src="/assets/images/project/potato-robotics/designIdeas.jpg"/>
    <figcaption class="caption">My Notes on Possible Ideas</figcaption>
</div>

<br/>

After several group discussions, we decided to go with the **claw + dragging-the-bin** design.

# Mechanical Design

We used **onShape** for our CAD prototyping and simple materials like cardboard, pop sticks and straws for physical prototyping. The robot can be divided into two parts: the **chassis** and the **claw**. 


## Chassis Design

The main design criteria are as followed: 
- **weighted approppriately**: so that the tires can generate enough friction without consuming too much power
- **firm**: able to tow the bin and operate without deforming
- **compacted**: has the appropriate length and width to centralize the center of mass, and able to contain all electrical and mechanical components

<div class="side-by-side-normal">
    <div class="toleft">
        <img class="image" src="/assets/images/project/potato-robotics/lifterCad2.png">
    </div>
    <div class="toright">
        <img class="image" src="/assets/images/project/potato-robotics/lifterCad3.png">
    </div>
</div>
<figcaption class="side-by-side-caption">CAD Prototyping for Our Initial Idea</figcaption>

<br/>

<div class="wrapper-normal">
    <img class="image" src="/assets/images/project/potato-robotics/mydesignCAD.gif"/>
    <figcaption class="caption">CAD Prototyping of the Chassis in My Design</figcaption>
</div>


## Wheel Design
The radius of the wheels is determined by the weight of the robot, the roughness of the tires, and the total energy loss when the robot is moving (wheels, motors, dissipated heat, weight distribution, etc.). To obtain a practical wheel radius, I wrote a MATLAB script that plots the optimal wheel radius. (source: [this research][2])

{% highlight MATLAB %}
motor_torque = rolling_force.*wheel_radius;
speed_rpm = -slope.*motor_torque + max_speed;
speed_linear = speed_rpm.*rpm_to_rpsec.*wheel_circumference;

plot(2.*wheel_radius/cm_to_m, speed_linear/cm_to_m);
{% endhighlight %}

<div class="side-by-side">
    <div class="toleft">
        <img class="image" src="/assets/images/project/potato-robotics/WheelDiameter_vs_RobotSpeed.png">
        <figcaption class="caption">If weight is distributed 1/4 on each rear wheel&nbsp;&nbsp;&nbsp;</figcaption>
    </div>
    <div class="toright">
        <img class="image" src="/assets/images/project/potato-robotics/WheelDiameter_vs_RobotSpeed2.png">
        <figcaption class="caption">If weight is distributed 1/3 on each rear wheel</figcaption>
    </div>
</div>

## Claw Design




:warning: Still Under Construction! :warning:

[1]: https://docs.google.com/presentation/d/1NzWH9MaUuBmohNG058sFNCbh795XGM3u00Jgc1uZl1A/edit?usp=sharing
[2]: https://dspace.mit.edu/bitstream/handle/1721.1/92068/897211724-MIT.pdf;sequence=2