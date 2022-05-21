---
title: Sony Pictures Imageworks
layout: post
date: 2020-01-01
image: /assets/images/work/Imageworks_logo.png
headerImage: false
tag:
- Python
- Javascript
- GJS
- GCP
- C++
- Gnome
- App Script
- API Dev
# category: blog
author: daniel
position: Full Stack Software Engineer
time: 2021-05-01 - Present
start-date: 2021-05-01
isCurrent: true
---

# Introduction

## About the Position

As a software engineer intern in Sony Pictures Imageworks, one of the biggest VFX companies in the world, I was fortunate enough to not only be part of the amazing and friendly dev group, but also enjoy the best coop experience I have ever had. I work in **APPs group under Infrastructure**, which primarily develops internal applications to improve artist/employee workflow. 

## Confidentiality
Since the films we make are highly confidential for not only us, but most importantly, the company's clients - one small leak of any detail of the movie will mess up their whole marketing scheme and cost them tens of thousands/millions of dollars (just for example, the latest *Spiderman No Way Home*, as well as most of the previous Spiderman movies). **Roughly 80-90% of the applications** the employees and artists use are completely internal with limited access to the internet. Moreover, any software request (python package installation, internet access to a specific tool, etc.) requires the approval of the security team. The more access the software might need, the higher the level of approval is required. 

Here, you will find the rough purpose/outline of my projects at Sony Pictures Imageworks without the minor details, due to confidentiality. 


<br/>

# Projects


<section class="list">
    {% for post in site.posts reversed %}
        {% if post.category == 'sony' %}
            <div class="item {% if post.star %}star{% endif %}">
                <a class="url" href="{% if post.externalLink %}{{ post.externalLink }}{% else %}{{ site.url }}{{ post.url }}{% endif %}">
                    {% if post.imagewidth %}
                        <img src="{{ post.image }}" style="width:{{ post.imagewidth }};" class="projectImgWidth">
                    {% else %}
                        <img src="{{ post.image }}" class="projectImg">
                    {% endif %}
                    <aside><time datetime="{{ post.date | date:"%d-%m-%Y" }}">{{ post.date | date: "%b %Y" }}</time></aside>
                    <h3 class="title">{{ post.title }}</h3>
                    <p>{{ post.description }}</p>
                    {% include post-tags.html ignore_job_type=true %}
                </a>
            </div>
        {% endif %}
    {% endfor %}
</section>

# Data Studio Application Usage / Data Analysis



:warning: Still Under Construction! :warning: