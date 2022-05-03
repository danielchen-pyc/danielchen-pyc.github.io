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
hidden: show
---

# Project Goal
The ultimate goal for this course is to build a **fully autonomous** aluminum can collector that will be able to compete in the 253/480 Robot Competition. There are some key features that the robot must be able to perform:
- Line following
- Can detection
- Can collection
- Basic self-localization
- Return to starting position

The robot also needs to be **constructed entirely from scratch**, from [prototyping](#prototyping), [mechanical design](#mechanical), [electrical design](#electrical), to [software programming](#software). 


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

The two main electrical components in this project are [H-Bridge](#hbridge), [IR detector](#IR-detector) and [Control](#control).

<h2 id="hbridge">H-Bridge Circuit</h2>

<div class="wrapper-normal">
    <img class="image" src="/assets/images/project/potato-robotics/bluepill.png"/>
    <figcaption class="caption">STM-32 Blue Pill Microcontroller Board</figcaption>
</div>

<br/>
<div class="wrapper-normal">
    <img class="image" src="/assets/images/project/potato-robotics/h-bridge.png"/>
    <figcaption class="caption">H-Bridge Circuit Layout</figcaption>
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
*Side Note: I also found this little power clip really useful :point_down: (reduces cluttering)* :) 
<div class="wrapper-normal">
    <img class="image" src="/assets/images/project/potato-robotics/powerclips.png">
    <figcaption class="caption">Male and Female Power Cable Connectors</figcaption>
</div>


<h2 id="IR-detector">IR Detection (Tape Following)</h2>

The robot also has to be able to maneuver itself around the competition arena, and the best way for it to do so is adding the tape following feature. The only approved way to guide this robot is via black tapes (no remote control allowed for it to be completely autonomous). We have two phototransistors (`TCRT5000 Reflective Optical Sensor with Transistor Output`) in front of the robot that detects the black tape on the ground. It will signal the robot to stay on the black line as it moves. 

<div class="side-by-side">
    <div class="toleft" style="width: 40%;">
        <img class="image" src="/assets/images/project/potato-robotics/LightsensorCircuitdrawn.png">
        <figcaption class="caption">Light Sensor Circuit Layout</figcaption>
    </div>
    <div class="toright" style="width: 55%;">
        <img class="image" src="/assets/images/project/potato-robotics/lightsensorcircuit.png">
        <figcaption class="caption">Light Sensor Circuit</figcaption>
    </div>
</div>

<div class="wrapper-normal">
    <img class="image" src="/assets/images/project/potato-robotics/lightsensor.png">
    <figcaption class="caption">Two Light Sensors</figcaption>
</div>


<br/>

Controlling the robot using two light sensors can actually be challenging, that is why we need [PID control](#control) to navigate the robot smoothly.


<h2 id="control">PID Control</h2>

PID is a crucial concept in control systems which stands for Proportional, Integral, and Derivative. If we just map the values captured by the phototransistors **linearly** to the rotational speed of the wheels, the robot will move in a very unstable fashion (jiggling side to side a lot). We want the robot to able to not only generate a smooth, stable trajectory, but also knows how to react when the black tape directs it to a sharp turn. We introduce feedback loops that consist of fine-tuned gains (proportional, integral, derivative) to control the final output of the wheel speed. 

<div class="wrapper-normal">
    <img class="image" src="/assets/images/project/potato-robotics/pidexample.png">
    <figcaption class="caption">PID Explanation</figcaption>
</div>

<br/>

| **Situation** | &nbsp;&nbsp; **Left Sensor** &nbsp;&nbsp; | &nbsp;&nbsp; **Right Sensor** &nbsp;&nbsp; | &nbsp;&nbsp; **Robot Offset from Tape** &nbsp;&nbsp; | **Response** |
|:-------------:|:------------:|:------:|:------:|:------:|
| Both Sensor on Tape | 1 | 1 | 0 cm | Straight |
| Left On, Right Off | 1 | 0 | 1 cm | Slight Left |
| Left Off, Right On | 0 | 1 | -1 cm | &nbsp;&nbsp; Slight Right &nbsp;&nbsp; |
| &nbsp;&nbsp; Both Sensor Off (Left was last on) &nbsp;&nbsp; | 0 | 0 | >= 5 cm | Left |
| Both Sensor Off (Right was last on) | 0 | 0 | <= -5 cm | Right |

<figcaption class="caption">Steering using Sensor Output</figcaption>

Now that we have the electrical portion set up, we need software programming to actually analyze the light sensor outputs and perform the PID control.

<br/>

<h1 id="software">Software Construction</h1>

Our software is written in primarily **C++**. I divided the robot into three main classes ([DriveSystem](#driveSys), [SonarSystem](#sonarSys) and [ClawSystem](#clawSys)) and added subclasses as needed. 

<div class="wrapper-medium">
    <img class="image" src="/assets/images/project/potato-robotics/software.png">
    <figcaption class="caption">Software Construction Diagram</figcaption>
</div>

<br/>
<h2 id="driveSys">Drive System Code</h2>

`DriveSystem` controls the motors, i.e. speed control, steering, brake, etc. It has a child class `Motor`, which mainly commands the wheels to spin forward or backwards. There is another class under `Motor`, `PwmActuator`, which performs lower level communications with the bluepill board, such as reading and writing to a specific pin. The wheel speed can be directly regulated by the analog value written to that specific pin and the h-bridge is designed to draw power to supply the motors according to that analog value. For example, rotating can be commanded by setting one side 0 and another side `ROTATE_SPEED`.

{% highlight c++ %}
void DriveSystem::rotate_left() {
    this->update(0, ROTATE_SPEED);
}

void DriveSystem::rotate_right() {
    this->update(ROTATE_SPEED, 0);
}
{% endhighlight %}
<figcaption class="caption">Functions for rotating left/right</figcaption>

<br/>
<h2 id="sonarSys">Sonar System Code</h2>

There are three sonars that detects cans: front, left, right. If any one of the sonars detects an obect closer than around half a meter (50 cm), it would initiate a process that collects the cans. 

{% highlight c++ %}
SonarSystem::SonarSystem(int left_trigger, int left_echo, int front_trigger, int front_echo, int right_trigger, int right_echo) {
    SonarSystem::sonar_left = new NewPing(left_trigger, left_echo, MAX_DISTANCE);
    SonarSystem::sonar_front = new NewPing(front_trigger, front_echo, MAX_DISTANCE);
    SonarSystem::sonar_right = new NewPing(right_trigger, right_echo, MAX_DISTANCE);
}
{% endhighlight %}
<figcaption class="caption">Function for initializing all three sonars</figcaption>

<br/>
<h2 id="clawSys">Claw System Code</h2>

Claw system is initiated when the robot starts and will be activated whenever the sonar system detects a can that is close enough to the robot. It controls a motor that lifts and drops the claw, and another motor that open and closes the claw. 

{% highlight c++ %}
void ClawSystem::open_claw() {
    this->claw_servo.attach(this->claw_pin);  
    delay(200);
    for (int servoPos = 90; servoPos >= 59; servoPos--) {
        this->claw_servo.write(servoPos);
        delay(33);
    }
    for (int servoPos2 = 59; servoPos2 <= 90; servoPos2++) {
        this->claw_servo.write(servoPos2);
        delay(32);
    }
    this->currentPos = "open";
    this->claw_servo.detach();
}

void ClawSystem::grab_can_sequence() {
    this->open_claw();
    this->lower_arm();
    this->grab();
    delay(100);
}
{% endhighlight %}
<figcaption class="caption">Functions for Grab-Can-Sequence</figcaption>


# Final Results and Thoughts

Eventually, all of our robots are able to successfully follow the tape and collect cans! I had multiple 10/10 runs before the competition and was really proud of what I have achieved and the skills that I have learnt. We got into top 8 in the final competition and was tied at the fifth place. Despite of making our individual robots during the pandemic, I am still extremely satisfied with the results and our accomplishments. 

Making robots is really fun! :robot:


[1]: https://docs.google.com/presentation/d/1NzWH9MaUuBmohNG058sFNCbh795XGM3u00Jgc1uZl1A/edit?usp=sharing
[2]: https://dspace.mit.edu/bitstream/handle/1721.1/92068/897211724-MIT.pdf;sequence=2
[3]: https://www.sciencedirect.com/science/article/pii/S2212827116307417#:~:text=Axiomatic%20Design%20principles%20were%20employed,adaptability%20on%20oddly%20shaped%20objects.