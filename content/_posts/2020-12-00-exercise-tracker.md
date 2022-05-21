---
title: ":runner: Exercise Tracker"
author: daniel
layout: post
date: 2020-12-23 22:10
tag: 
- JavaScript
- MongoDB
- ExpressJS
- ReactJS
- NodeJS
image: /assets/images/project/mern-exercise/exercise.gif
headerImage: true
projects: true
description: "MERN Stack App for Tracking Daily Exercises"
category: project
externalLink: false
source: learn-MERN-stack
hidden: show
---

# Motivation
In the term 1 of my third year, I joined [UBC LaunchPad Software design group][1]. We were developing a [webapp][2] for facilitating course registration in UBC. I never had any experience with webapps at that time so I was primary working on the backend with a little bit frontend. I was never involved in the development of the webapp architecture. 

During the winter break between term 1 and term 2, I thought it would be nice if I can learn more about MERN stack webapps. 

# Language and Tools
- Javascript
- MongoDB
- Express
- Node
- React
- Bootstrap

# Implementation
I used `Express` to create some routers for the `POST` and `GET` requests for supporting the following functionalities: add user, add exercise event, delete exercise event, and edit exercise event. The exercise events and users will be stored in the `MongoDB Atlas` database via `mongoose`. The website is running on `Node` and the UI is built using `React`, designed using `Bootstrap` and `HTML`. 

[comment]: # (TODO: Add diagram for routers)

The user and exercise events are formatted in `JSON` before storing it in `MongoDB`. The data is sent in the format as shown: 

{% highlight JSON %}
{
	"username": "Daniel",
	"description": "Running",
	"duration": "30",
	"date": "2020-12-21T02:38:31.105Z"
}
{% endhighlight %}

The same data is stored in `MongoDB` in the following format: 
{% highlight JSON %}
{
    {
        "_id":{"$oid":"61a4076f80f9d56eb9c60090"},
        "username":"Daniel",
        "description":"Running",
        "duration":{"$numberInt":"30"},
        "date":{"$date":{"$numberLong":"1638139752718"}},
        "createdAt":{"$date":{"$numberLong":"1638139759477"}},
        "updatedAt":{"$date":{"$numberLong":"1638139759477"}},
        "__v":{"$numberInt":"0"}
    }
}
{% endhighlight %}

![Architecture][3]
<figcaption class="caption">Simple UI for the MERN exercise tracker</figcaption>

# Thoughts
It was a fun experience gaining more knowledge of MERN stack and `Javascript` in general! To me, the whole router thing was really abstract at first but I was able to understand the underlying structure of it, which is good :slightly_smiling_face:. It is also a very good skill to have, not like I'm gonna be a WebApp developer but maybe there will be some projects in the future that requires a webapp to visualize it. 

☺

[1]: /designTeams/launchpad
[2]: https://github.com/ubclaunchpad/life-at-ubc
[3]: /assets/images/project/mern-exercise/demo.png