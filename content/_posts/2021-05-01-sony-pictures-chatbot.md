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

The original version of that project worked fine in most cases, but there were some limitations. First of all, the pipeline for updating information to the database is a little buggy, some information was not being updated. Secondly, it doesn't recognize the target user if their name is mispelled, because the program uses an almost 1-1 mapping to retrieve the info and all methods were done programmatically. Lastly, it shows all the info of the target user, causing it difficult for the user to find the information they really want. When I got assigned to the project, these were the main issues I was aiming to solve. 

To sum up, I needed to improve upon these following points:
- Fix pipeline bugs
- Improve text recognition by improving the mapping mechanism
- Add specific info functionality

<br/>

# Problem One: Pipeline Bugs

I won't go into the details of solving the bug, but this is a good opportunity to describe the infrastructure of the program! We are using **Google Cloud Platform (GCP)** and its services as the backbone of the project. We created **service accounts** for every other external or internal services that wants to connect to or have access to this project or its data. Since the company already uses most of the Google services, we use `gchat` as the main text-based communication tool within the company. This project is a `gchat bot` that can be invited to a group or message it directly to use its functions. 

## Structure

The `gchat bot` is written in **Javascript** and running on **Google App Script**. One can use Google App Script (GAS) and all the APIs provided by Google to access Google services programmatically, including continuously updating Google sheets, monitoring Gmail, modifying Google Drive, and a lot more. Our bot is created using the `Hangout Chat API` provided by GCP and retrieves data stored in a Google NoSQL database called `Firestore`. The data in Firestore is updated everyday by a cronjob written in **Python**. This cronjob obtains its data from a reliable, up-to-date source so that when the user queries the chatbot, all the information shown is accurate.

## Pipeline Bugs

During the whole project, which lasted around 2-3 months, I learned a lot about debugging Google App Script and GCP projects. There are some bugs that result from version conflicts of the old and new GAS (very weird), and sometimes GAS overwrites your config file (without asking your permission :rage::rage:) when you rebuild the project, causing the whole bot to crash. All debugging tips have been documented for the next *bot developer*! 


<br/>

# Problem Two: Improve Searching Mechanism

Just like any web browser or search engines, the bot must be able to handle mistyped names or IDs and try its best to guess what the user is really trying to ask. It should be able to process most real life cases - from simple mistyped words to complicated grammar mistakes. The original method was to brute force and process the query by taking care of some simple cases, but it covers a very small number of mistypes/mistakes. We need a proper **natural language processing (NLP)** method in order to cover most of the cases.

## DialogFlow

**DialogFlow** is a Google service that allows developers to train its own ML model for their NLP applications. Moreover, DialogFlow is also a lifelike conversational AI with state-of-the-art virtual agents. It supports NLP over 30 languages and also provides **speech-to-text recognition**. Developers can also use it to perform **context and sentiment analysis**, and subsequently improving their NLP predictions. 

#### How does it Work?

The main concepts of DialogFlow are **entities and intents**. You can train most of the ML model by providing accurate training data for those two categories.

![work][1]
<figcaption class="caption">Illustration of how DialogFlow works</figcaption>

## Integrating with the Bot

*Now, this is the tricky part of the whole project.* Since DialogFlow was just a new tool/concept for most people in the company, I was assigned to explore it and examine the possibilities of using it in this project. 

#### Challenge

There are many examples where people integrated DialogFlow into a chatbot **directly**, meaning that the output of the ML model in DialogFlow is precisely the response of the chatbot (text). In our case, the output of the chatbot is not purely text, it's a [response card][2], which might consists of text, images, external links, or different sections of text. 

In the Hangout Chat API, you can only choose one - **either connect the chatbot to DialogFlow, or to Google App Script**. However, we need DialogFlow's NLP functionalities, and we also need GAS to fetch some of the data that cannot be done using DialogFlow and create a response card. **We decided to use GAS as the main connection to the chatbot, and connect GAS to DialogFlow by ourselves.**

![hangout api][3]
<figcaption class="caption">You can only choose one! </figcaption>


#### Using the API to Connect GAS with DialogFlow

We use a third party API that assists Google API Connection called `cGoa`. `cGoa` helps connect GAS to DialogFlow and combining that with the [`DialogFlow API`][4], we can perform all functionalities of DialogFlow **programmatically**.

Steps:
- Use cGoa to connect to service accounts (for GCP access)
- Connect to Google Drive API to upload service account credentials (once)
- Connect to DialogFlow API
- Use Hangout-DialogFlow connection to communicate between two backends (GAS and DialogFlow)

<br/>

{% highlight javascript %}
function detectIntent(message, optLang){
  var goa = cGoa.GoaApp.createGoa('dialogflow_serviceaccount',
                                   PropertiesService.getScriptProperties()).execute();
  if (!goa.hasToken()) {
    throw 'something went wrong with goa - no token for calls';
  }

  Dialogflow.setTokenService(function(){ return goa.getToken(); } );

  var requestResource = {
    "queryInput": {
      "text": {
        "text": message,
        "languageCode": optLang || "en"
      }
    },
    "queryParams": {
      "timeZone": Session.getScriptTimeZone()
    }
  };

  var PROJECT_ID = 'xxxxx'; // your Dialogflow proejct ID (in DF panel -> Setting)
  var SESSION_ID = encodeURIComponent(Session.getTemporaryActiveUserKey()); 
  var session = 'projects/'+PROJECT_ID+'/agent/sessions/'+SESSION_ID; // 
  var intent = Dialogflow.projectsAgentSessionsDetectIntent(session, requestResource, {});
  return intent;
}
{% endhighlight %}
<figcaption class="caption">The function that outputs the response from DialogFlow programmatically! </figcaption>

The [DialogFlow API][4] is quite extensive! You can not only train the model programmatically, but also create project, entity types, delete training data, etc.

I trained the model so that the response of DialogFlow is prefixed with a certain keyword, depending on the type, and when the response gets passed to GAS, I use the prefix to determine which response card / data is suitable for that query. 

The structure of the program has now changed drastically compared to the original version. In the original version, if a user wants to look up `Daniel Chen`'s (me!) phone, he/she would need to type precisely `Daniel Chen` without any mistakes. Now, if you type `Danniel Chan`, you will most likely be able to get the results you want. When the bot gets a message, **it sends the message to DialogFlow using the API, gets the NLP response (containing keywords), parse the message to get relevant info, fetch necessary info from database, format response card and send the response card.** 

[Similar Tutorial!][5] 

#### Other APIs

<div class="wrapper-medium">
    <img class="image" src="/assets/images/work/sony-pictures/chatbot/api.png">
    <figcaption class="caption">Other APIs that I used!</figcaption>
</div>

- [Firestore GAS API][7]
- [cGoa][8]

<br/>

# Problem Three: Showing Unnecessary Info

If a user asks for person A's phone, it would show Person A's whole info. Solving this problem would be a lot more difficult before using DialogFlow, because you would have to consider grammar mistakes, the user's sentiment, etc. Now, all we have to do is create multiple training phrases for each functionality (eg. phone number).

- "What is daniel's phone"
- "I want to call daniel"
- "Can you give me daniel chen's phone please"
- ... and more

The output will be `PHONE: Daniel Chen` and the functions I wrote in GAS will be able to parse this output from DialogFlow and fetch Daniel Chen's phone. 

Adding DialogFlow really increases the whole project's scalability and user friendliness. The users will now be able to actually "chat" with the chat bot rather than using a specific query format.

# Thoughts

This project really improved my `javascript` and `python` programming and debugging skills, especially `javascript` since I didn't know much about it before. I also created a lot of cronjobs to automate the project's workflow, making the project run normally without intensive human interventions. Because of that, I also gain a lot of experience in **designing and controlling the project's workflow, pipelining and automation**. Implementing, or hacking :sweat_smile:, GAS and connecting all those APIs was extremely challenging because not many people have done it and there isn't a lot of examples available online. Nonetheless, I learned a lot about exploring different methods and debugging as I code because of the challenges I faced. Having this valuable experience, I will not be afraid to try out new things next time!


[1]: /assets/images/work/sony-pictures/chatbot/how-does-it-work.png
[2]: https://developers.google.com/chat/api/guides/message-formats/cards
[3]: /assets/images/work/sony-pictures/chatbot/hangout-api.png
[4]: https://developers.google.com/resources/api-libraries/documentation/dialogflow/v2/python/latest/index.html
[5]: https://github.com/mhawksey/Hangouts-Chat-bot-with-Dialogflow
[6]: /assets/images/work/sony-pictures/chatbot/api.png
[7]: https://github.com/grahamearley/FirestoreGoogleAppsScript
[8]: https://github.com/brucemcpherson/cGoa