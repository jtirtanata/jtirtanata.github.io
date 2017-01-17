---
layout: post
title: My First Post
tags: mta groupwork pandas data
author: Josephine Tirtanata
---
## Project Benson
Metis was not kidding when they said they will kick things off from day 1. On our first day, we coded, learned [pandas](http://pandas.pydata.org/), and received a project for the week. This project is called *Project Benson*, and the task is to use [MTA Turnstile Data](http://web.mta.info/developers/turnstile.html) data to come up with location recommendations for WTWY street teams to collect email addresses and potential attendees for their gala.

>WomenTechWomenYes (WTWY) has an annual gala at the beginning of the summer each year. As we are new and inclusive organization, we try to do double duty with the gala both to fill our event space with individuals passionate about increasing the participation of women in technology, and to concurrently build awareness and reach.  

>To this end we place street teams at entrances to subway stations. The street teams collect email addresses and those who sign up are sent free tickets to our gala.

### Data Cleanup
At first, I was impressed that MTA has this amount of data accessible by anyone on the planet. Granted, they have *a ton* of data - you do not want to know how long it took me to create a dictionary with a single data file.

Unfortunately, this feeling did not last long, as I began to discover the many quirks that made data cleaning quite tricky. First, counting the entry counts gave a lot of negative values. One of our classmates, Dexter, proposed an interesting theory to why that happens. The turnstile number might have been too large that it started subtracting 1 from the count instead of adding it up. By this assumption, we simply take the absolute number of these values. We also removed numbers that are outliers.

Next problem is, the time intervals. The bulk of data is taken every 4 hours; at 4:00, 8:00, 12:00, 16:00, 20:00, and 0:00. But, we also found stations that took count's at other hours, and with different time intervals. There was a significant number of entries that were taken every 8 hours, and it varied until 12 hours. I really wanted to normalize the entry counts to some kind of pace unit, (e.g. counts/hour) but after some discussion with the team, we decided to remove these data for the sake of time.

### Approaches
Through sorting and plotting, we were able to get the busiest stations pretty quickly. The obvious names came up: Harold Square, Times Square, Penn Station, etc. We didn't like the fact that these stations came up because not only are they awfully crowded, they are also not where our target populations live or work. These kind of stations, given that this is New York, are full of tourists. And, in the case of Penn Station or Port Authority, they are the stations that people living out of town go to to take the train or the bus. We also felt very strongly about crowded stations as ineffective for the canvassers because one, people are in a hurry and unlikely to stop and two, it would be very difficult for them to give out information.

These assumptions led us to our next approach, which is to find the entry exit ratio in weekday mornings. Higher entries in the morning suggests residential areas, and higher exits, commercial areas. After segregating the stations to these two categories, we came up with different approaches for weekends versus weekdays.

In the weekdays, we assume that workers are unlikely to stop in the morning, (people are always in a hurry to go to work) nor afternoon (people are always in a hurry to go home too). We decided that the best times to visit business areas is during their lunchtimes. Some of our teammates, also pulled up the data on where tech companies are, and based on these two categories, we made our recommendations for targeting business areas.

For the weekends, we wanted to target areas with high likelihood to be residential, and which has the highest proportion of wealthy tech workers. We were able to get the percentage of tech workers for each station, and after multiplying this value to the entry counts of the stations, we were able to get a sense of how much of our target population are in these areas.

### Fuzzy Wuzzy
This was a really cool library that I manage to use for the project. Given two words, fuzz was able to score how similar they are.

Given that we had 2 datasets we were working with, we had trouble trying to merge these together. The problem being, the census data had a different format of subway names.

With fuzzywuzzy, we were able to find the best match of each of the station names in the census data set, with the mta turnstile data.  

```
name_mapping = {}
names = df['COMPLETE_NAME'].unique()
for name in df_income['COMPLETE_NAME'].unique():
    score = process.extractOne(name, names, scorer=fuzz.token_sort_ratio)
    name_mapping[name] = score[0]
```


Funnily, we used fuzzywuzzy to merge subway stations that are the same (some of the data that were referring to the same stations have different line names), and it was only when I did the challenges at the end of the day, I found that I could've just *groupby-ed* the C/A, Unit and Station values to get the same results.

## Metis

### How I feel about my first week
I had a hypothesis that I would like data science, given that I am a programmer, and I love math. The first week here confirmed that for me. I'm happy that I've made the decision to resign from work and go back to school.

I also find these We Work buildings to be very interesting. I like how there's a lot of community events and that the sort of people working in We Work are related to tech.

### Group work
I have some general thoughts about the week of group work I just went through. In college, I've always found group projects to be frustrating. I felt like they're so easy to mismanage, that working in groups end up not as productive as working alone. I liked the fact that in my previous work, I mostly get bugs or features that I can work on end to end by myself. The point I'm trying to make is, my thoughts about group work is different after this week. Even though we had a lot of arguments, I believe it was the arguing back and forth that really pushed me to question my ways and allow me to work better than I would've been if I had worked by myself.
