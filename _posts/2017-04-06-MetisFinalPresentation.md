---
layout: post
title: Metis Final Presentation
tags: presentation metis
author: Josephine Tirtanata
---
It was hard to believe that my 12 weeks in Metis was coming to a close. At this point, we were used to presenting in front of class. The final presentation however, had other people aside from our classmates; recruiters, alumni and family members and friends of our classmates. We also had a podium and a microphone, and it wasn't until the second rehearsal that I got the hang of holding the microphone, and also, got enough practice to hide my nerves. You have to know, my presentation experience coming in to Metis is approximately 0 - the idea of presenting to a big crowd freaks me out. The silver lining is, we don't have to answer questions this time around (something we *have* to do in regular in class presentations)!


<img src="/images/me.png" />


Enough about my stage dilemmas, what did I present on?

## Beauty Bot
This is the title of my project. My goal for the last three weeks of Metis is to improve the buying of makeup. It started with me feeling like I always have difficulties buying make up online. I hardly do it, because I have to try out the shades that goes with my skin, and to see and feel how that product will go on me. I buy products I've tried and loved, and that's pretty much it. Then, I also thought about the difficulty of buying make up offline. There's so many products out there; I can spend 2 hours in Sephora, and still feel like I haven't found a product that suits me. That brings me to my last point, there's a lot of skin varieties and types; making it harder for anyone to find the right match of products for their skin types. If I was able to create an AI that can match your skin tone to products, *and* preview how such make up can look on you, then, that AI would also be smart enough to curate products that is suitable for you.

### The recommendation app
The recommendation app is hosted in [beautybot.io](http://www.beautybot.io). At the time of this writing, the domain is bought and owned by me, but the website is not yet completed. Be sure to check in after a couple of weeks!

There is two parts to the recommendation app.

#### 1. Autoencoder recommendation model
Online stores like [Sephora](www.sephora.com) recommends products that is popular, taking into account what you've bought in the past and the ratings you gave that product, if you did rate them. With my app, I wanted to emphasize when users have a *negative* experience. With skin products, you might have certain allergies or certain colors and textures that makes a foundation or lipstick unsuitable for you. If you have similar dislikes with another person, it can warn you of products that you should not buy. Therefore, my application takes in not only what you've liked in the past, but also what you've disliked, and then recommends only the ones that *will* work well. You can read more about the model [here](/BeautyBot).

#### 2. Skin Tone Matcher
This was actually the simplest model of the three (read on to find out about the third one). This is a simple 3 image classification problem. However, you have the loss as `mse` (mean squared error) instead of `categorical_crossentropy`. This took less than 30 minutes to train in a GPU.

Here's a walkthrough of the app.

<iframe width="560" height="315" src="https://www.youtube.com/embed/pdXjktu5zFk" frameborder="0" allowfullscreen></iframe>

### Virtual Make Up
The virtual make up experience is a proof of concept that I go into more detail [here](/StyleTransfer). It takes around 10 minutes to style a set of eyes, which is too long for production standards. Hopefully, there will be better processing power in the future that can allow faster image transformations. Although, 10 minutes is still faster than the line in Sephora, right?
