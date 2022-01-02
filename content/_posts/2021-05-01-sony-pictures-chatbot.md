---
title: ":robot: Machine-Learning Chatbot for Querying Employee Info"
author: daniel
layout: post
date: 2020-05-01 22:10
tag: 
- ML
- Javascript
- Python
- GCP
- Google API
- DialogFlow
- App Script
hidden: true
image: /assets/images/work/sony-pictures/chatbot/icons.png
headerImage: true
projects: true
description: "Internal Google Chatbot that facilitates Company Workflow by Obtaining Relevant Info"
category: sony
externalLink: false
---


# Introduction

On account of the pandemic, most of the employees (90%+) in the company started to work from home. Not only did it reduce reduce social interactions but also increase the difficulty of finding co-workers' info since we cannot just go and ask them physically. Simple questions such as asking for linux machine number, the department of a particular colleague, someone's phone number, etc. have become very difficult during the pandemic. The development group started a project that allows employees to simply type in the user's name / ID to obtain all relevant information about that specifc user, including their title, department, current tasks, phone, office, etc. The information displayed can also be modified by the users themselves.

<br/>

# Limitation

The original version of that project worked fine in most cases, but there were some limitations. First of all, the pipeline for updating information to the database is a little buggy, some information was not being updated. Secondly, it shows all the info of the target user, causing it difficult for the user to find the information they really want. Lastly, it doesn't recognize the target user if their name is mispelled, because the program uses an almost 1-1 mapping to retrieve the info and all methods were done programmatically. When I got assigned to the project, these were the main issues I was aiming to solve. 

To sum up, I needed to improve upon these following points:
- Fix pipeline bugs
- Add specific info functionality
- Improve text recognition by improving the mapping mechanism

<br/>

# Problem One: Pipeline Bugs

I won't go into the details of solving the bug, but this is a good opportunity to describe the infrastructure of the program! We are using **Google Cloud Platform (GCP)** and its services as the backbone of the project. We created **service accounts** for every other external or internal services that wants to connect to or have access to this project or its data. Since the company already uses most of the Google services, we use `gchat` as the main text-based communication tool within the company. This project is a `gchat bot` that can be invited to a group or message it directly to use its functions. 

## Structure

The `gchat bot` is written in **Javascript** and running on **Google App Script**. One can use Google App Script (GAS) and all the APIs provided by Google to access Google services programmatically, including continuously updating Google sheets, monitoring Gmail, modifying Google Drive, and a lot more. Our bot is created using the `Hangout Chat API` provided by GCP and retrieves data stored in a Google NoSQL database called `Firestore`. The data in Firestore is updated everyday by a cronjob written in **Python**. This cronjob obtains its data from a reliable, up-to-date source so that when the user queries the chatbot, all the information shown is accurate.

During the whole project, which lasted around 2-3 months, I learned a lot about debugging Google App Script and GCP projects. There are some bugs that result from version conflicts of the old and new GAS (very weird), and sometimes GAS overwrites your config file (without asking your permission :rage::rage:) when you rebuild the project, causing the whole bot to crash. All debugging tips have been documented for the next *bot developer*! 




