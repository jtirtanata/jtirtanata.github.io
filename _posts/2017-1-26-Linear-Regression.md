---
layout: post
title: Using Linear Regression to Investigate NBA Statistics
tags: linearregression nba statistics sports modelling webscraping
author: Josephine Tirtanata
---

## Webscraping
I find webscraping a fun exercise. I've done it in the past, but I've never done it to this extent. Coming from a computer science background, it's interesting to find the ways websites discourage scrapers to parse information from their site. This makes it more challenging (and fun) for data scientists to get information from the web. My primary source for this project is [basketball-reference.com](basketball-reference.com). When you go to an individual player's page, Basketball Reference returns html with all but one table commented out. These comments were removed after the site fully loads, with javascript. These are the tables that contain the most information, including the college statistics I wanted. It was frustrating at first because I can't figure out why BeautifulSoup would only return one table when I clearly see there were multiple tables from Chrome's Developer Tools. After I realize what was going on, I resorted to Selenium to load the page, and then extracting the html to give to BeautifulSoup. It took me around 1 hour to scrape around 800 NBA players.

Fortunately, I found a work around (do tell me, if any of you have a better idea) the second time I scraped these pages(\*). I ended up being able to fetch the page with `requests` and using a simple string `replace` on `<!--` and `-->`(\*\*). I was able to successfully fetch my information without Selenium, scraping hundreds of pages in just under 15 minutes.

## Linear Regression
I spent some time thinking about what I want to predict. I wanted to make a model that would have a real life application. The March Madness is coming soon, and I thought about **predicting the performance of college prospects in the NBA**. I chose the Player Efficiency Rating (PER) as my output variable, because it effectively differentiates the good players.

## The data
I scraped approximately 1200 NBA players. These statistics were obtained from 1980 - 2016. I chose this range because the statistics before 1980 have a lot of sparse data.

## Fun facts
(that do not relate to my investigation, but are interesting nonetheless)

1. On average, a NBA player's wingspan is longer than the player's height.  
2. Michael Jordan was not #1 pick (what?!?!).  
3. Some players have negative PER (this is really rare).  
4. Most of the better players spend 1 year in college - except Tim Duncan.  
5. Here are some examples of low draft picks with PER in the lower range (i.e. busts)  
  - Adam Morisson (7.4 PER, Draft pick #3)
  - Hasheem Thabeet (10.3 PER, Draft pick #2)
  - Wesley Johnson (10.3 PER, Draft pick #4)
  - Anthony Bennett (10.3 PER, Draft pick #1)
6. 98/1190 players shoots with their left hand.   Nerlens Noel can shoot with both hands. Tristan Thompson switched from left to right.  
7. 1984 had the most players (4) with a PER of above 20.  

### PER
Predicting this turns out to be a difficult task. The R-squared comes to be about 0.2. Even though I wasn't able to make a predictive model, this investigation shed some light to the fact that your abilities to play in college does not translate directly to the NBA. Regardless, the factors that have the highest correlation includes field goal %, free throw %, steals, draft rank, and the age you enter the NBA. I decided to turn things around and explore what *is* predictable.  

### Rookie Salary
Rookie salary(\*\*\*) is a value that is predictable (R-squared = ~0.7). However this is only apparent for draft picks of 30 and below. When you investigate the numbers for the higher draft picks , there's a lot of variability that comes in to play (R-squared = ~0.01). The factors that is most correlated is 3-pt field goals, the year of the draft, and field goal%.  

### Conclusion
It's interesting to compare the features for salary and PER, because it shows the differences between what NBA recruiters *perceive* to be good qualities for the NBA versus what is *actually* a good predictor of success. For example, the ability to shoot 3 points  may highlight you as a player, in particular make you stand out in March Madness, but it is not a quality that translates well into the NBA. Furthermore, recruiters should put more attention to weight as it is a factor that negatively impacts your performance, probably because there's a higher chance that the player may get injured. I also found that defensive skills, such as blocks and steals, translate really well from college to NBA. Personally, I think that steals are a good indicator that you're a *smart* player who has a good knowledge of the game. Which is why, perhaps, shooting statistics are not as significant in my investigation for PER than those for salary.

\* This is the comment syntax for HTML  
\*\* I made two trips because there was data
 that I missed out on the first round  
\*\*\* First year salary
