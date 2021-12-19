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

The robot also needs to be **constructed entirely from scratch**, from [prototyping](#prototyping), [mechanical design](#mechanical), [electrical design](#electrical), to software programming. 


<h1 id="prototyping">Brainstorming Ideas</h1>

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



<h1 id="mechanical">Mechanical Design</h1>

We used **onShape** for our CAD prototyping and simple materials like cardboard, pop sticks and straws for physical prototyping. The robot can be divided into three parts: 

<ul>
    <li>
        <a href="#chassis"><strong>Chassis</strong></a>
    </li>
    <li>
        <a href="#wheels"><strong>Wheels</strong></a>
    </li>
    <li>
        <a href="#claw"><strong>Claw</strong></a>
    </li>
</ul>


<h2 id="chassis">Chassis Design</h2>

The main design criteria are as followed: 
- **weighted approppriately**: so that the tires can generate enough friction without consuming too much power
- **firm**: able to tow the bin and operate without deforming
- **compacted**: has the appropriate length and width to centralize the center of mass, and able to contain all electrical and mechanical components

<div class="side-by-side-normal">
    <div class="toleft">
        <img class="image" src="/assets/images/project/potato-robotics/lifterCad2.png">
    </div>
    <div class="toright">
        <img class="image" src="/assets/images/project/potato-robotics/lifterCad4.png">
    </div>
</div>
<figcaption class="side-by-side-caption">CAD Prototyping for Our Initial Idea</figcaption>

<br/>

<div class="wrapper-normal">
    <img class="image" src="/assets/images/project/potato-robotics/mydesignCAD.gif"/>
    <figcaption class="caption">CAD Prototyping of the Chassis in My Design</figcaption>
</div>


<h2 id="wheels">Wheel Design</h2>

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

<br/>

<h2 id="claw">Claw Design</h2>

These are the initial claw ideas we had. However, due to the material limitation, we couldn't make gears that is firm enough and able to smoothly operate. 

<div class="side-by-side">
    <div class="toleft">
        <img class="image" src="/assets/images/project/potato-robotics/claw.png">
    </div>
    <div class="toright">
        <img class="image" src="/assets/images/project/potato-robotics/claw2.png">
    </div>
</div>

After [some research][3], I finalized a claw design that is similar to the geared claw desgin, but is achievable with a strong enough structure and a double rail for the claws to slide on.

<div class="side-by-side">
    <div class="toleft">
        <img class="image" src="/assets/images/project/potato-robotics/linearClaw.png">
        <figcaption class="caption">Claw design proposed in the paper</figcaption>
    </div>
    <div class="toright">
        <img class="image" src="/assets/images/project/potato-robotics/linearClaw.jpg">
        <figcaption class="caption">Prototype of my claw design</figcaption>
    </div>
</div>

<div class="wrapper-normal">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/z9yNlK0SgWM?controls=0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    <figcaption class="caption">Final Claw Operating</figcaption>
</div>

<br/>

<h1 id="electrical">Electrical Design</h1>

In this project we have to design our own circuit for controlling the robot as well and the components are limited to basic electrical components (resistor, diodes, capacitors, AND/OR/NOT gates, etc.). **The steering of the robot is completely controlled by the angular velocity differences between the wheels and the angular velocity of the wheels is controlled by the current flowing through it.** We are not allowed to use pre-programmed or unauthorized motors or chips that facilitates this mechanism. 


We are using the `STM-32 Blue Pill Microcontroller Board` for the main controller of this project. To control 4 motors at the same time by regulating the amount of current through the motors, we use a **H-Bridge circuit** to achieve that. Normally, the H-Bridge circuit can be bought as a integrated chip, but in order to have a deeper understanding of circuit design, soldering and circuit debugging, we build our own. 

The two main electrical components in this project are [H-Bridge](#hbridge) and [IR detector](#IR-detector).

<h2 id="hbridge">H-Bridge Circuit</h2>

<div class="wrapper-normal">
    <img class="image" src="/assets/images/project/potato-robotics/bluepill.png"/>
    <figcaption class="caption">STM-32 Blue Pill Microcontroller Board</figcaption>
</div>

<div class="side-by-side">
    <div class="toleft" style="width: 40%;">
        <img class="image" src="/assets/images/project/potato-robotics/HBridge.png">
        <figcaption class="caption">H-Bridge Circuit Layout</figcaption>
    </div>
    <div class="toright" style="width: 55%;">
        <img class="image" src="/assets/images/project/potato-robotics/circuit-main.jpg">
        <figcaption class="caption">H-Bridge Circuit</figcaption>
    </div>
</div>


<div class="wrapper-normal">
    <img class="image" src="/assets/images/project/potato-robotics/circuit-main-3.png">
    <figcaption class="caption">H-Bridge Circuit with Annotations</figcaption>
</div>

<br/>
*Side Note: I also found this little power clip really useful (reduces cluttering)* :) 
<div class="wrapper-normal">
    <img class="image" src="/assets/images/project/potato-robotics/powerclips.png">
    <figcaption class="caption">Male and Female Power Cable Connectors</figcaption>
</div>


<h2 id="IR-detector">IR Detection (Tape Following)</h2>




:warning: Still Under Construction! :warning:

[1]: https://docs.google.com/presentation/d/1NzWH9MaUuBmohNG058sFNCbh795XGM3u00Jgc1uZl1A/edit?usp=sharing
[2]: https://dspace.mit.edu/bitstream/handle/1721.1/92068/897211724-MIT.pdf;sequence=2
[3]: https://www.sciencedirect.com/science/article/pii/S2212827116307417#:~:text=Axiomatic%20Design%20principles%20were%20employed,adaptability%20on%20oddly%20shaped%20objects.