---
layout: post
title: Kickstarter Prediction Model
tags: data classification
author: Josephine Tirtanata
---

## How to succeed in Kickstarter
I wanted to investigate that, given the project statistics, can I predict that the project will be successfully funded? If yes, it would gain a lot of insights to those who are interested in crowd funding, and/or to the numerous projects who cancelled their projects before the deadline ends.

### Data
I found an awesome [dataset](https://webrobots.io/kickstarter-datasets/)  , containing around 145k Kickstarter projects.

## Classification

### Logistic Regression
My first model is a simple logistic regression on the project goal amount (USD), live time (amount of time the project is live on the site), and the subcategories (for example: video documentary, video shorts, technology apps). This regression wasn't too shabby for a first model. I had an accuracy score of **73.6%**. This was a 20.9% increase from the dummy variable. My dataset was roughly equal, thus I wasn't too concerned with recall/precision. It wasn't surprising that logistic gave a goal amount a negative coefficient; the higher your goal, the harder it is to reach it. What I find odd is live time negatively correlated with success. At first, I thought, the project creators can change their deadline dates, but it turns out that as Kickstarter also mentioned here, projects with shorter deadlines tend to get funded more because there is a sense of urgency. I assume that, users who come across the project's page, seeing that the deadline is far away, will think of contributing later on but can potentially forget to do so. I also find odd, that technology does very badly in Kickstarter; having one of the worst failure rates compared to other categories. Kickstarter seems to be a place for **creative** project (design, dance, and theater does relatively well), and perhaps tech-related projects are not as welcomed (or trusted).   

This model does very well in general, but I found that it was pretty terrible on projects that have a lower goal amount. For this sub group, the accuracy score drops to a lousy 0.63, and this was very interesting to me. I spend a lot of time opening the pages for these projects, and I got a sense of what was happening. It seems that there's a *higher troll probability* in lower goal amounts. I found a lot of projects pledging $1 for various of ridiculous things such as [grilled cheese sandwiches](https://www.kickstarter.com/projects/1161808585/help-me-make-a-grilled-cheese?ref=nav_search) and [selfies](https://www.kickstarter.com/projects/illsucktoes/i-will-send-you-a-selfie-so-you-have-a-selfie-of-m?ref=nav_search).

### Modeling Lower Goal Projects
I scraped more data; going to the Kickstarter's website, and using selenium to grab a bunch of extra details about both the project and the creator's details. Good news, turns out the number of Facebook friends you have doesn't matter. I didn't see a correlation between quantity of friends with success/fail; *but* there is a higher chance of success if you connect your Facebook to Kickstarter.

#### Final model
I did a voting classifier that takes the best of 3 of these classifiers:

1. **SGD**
2. **SVM**
3. **Random Forest**


 I did SGD instead of Naive Bayes because SGD gave me better features while NB gave a lot of words that are common in both success and fail projects.

#### Score
The final prediction has a 0.77 accuracy. This was a huge improvement from the dummy model (58% in this sub category) and the first model (63%).
