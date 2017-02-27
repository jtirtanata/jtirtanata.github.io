---
layout: post
title: Visualizing Clusters
tags: clustering matplotlib plt seaborn
author: Josephine Tirtanata
---

## Challenges
Visualizing clusters are easy when you want have 2 or 3 numerical features that you
want to cluster on. But more often than not, there's a lot more than 3 features in your machine
learning clustering tasks. And since clustering is in the sphere of unsupervised learning,
there's not really a clear cut way to measure how good our clusters are. It is also difficult to
distinguish the differences between each clusters. Yes, we can skim through
the values for each cluster, and rely on our eyes to notice patterns, but I'm sure you agree
that's not really the best way to do it. However, I didn't want to delve into complicated D3.j visuals
to create these visualizations, I just wanted something fast to quickly sum up what my clustering model gave back to me. For these reasons, I will go through 2 simple functions I've created to make quick and helpful clustering visuals.

### Prologue
I created a print function to calculated the mean and medians of my cluster.

```python
def print_medians(df, columns, categories, label_col, metric='mean'):
    for label in df[label_col].unique():
        cur_type = df[df[label_col] == label]
        print('--------LABEL #{}---------'.format(label))
        for col in columns:
            if metric == 'mean' or metric == 'both':
                print('{} MEAN: {}'.format(col, cur_type[col].mean()))
            if metric == 'median' or metric == 'both':
                print('{} MEDIAN: {}'.format(col, cur_type[col].median()))
        print('****Categories****')
        for cat in categories:
            print(cur_type.groupby(cat).size())
```
Usage: This function works on a pandas dataframe, where `columns` is the name of the numerical features that you want the median/mode on, and `categories` is the column names of the category that you wan't to see the count in for each cluster. `label_col` is the label that gives the number of different clusters.
This was a good start, I can see differences in the means of features between clusters when I had 5 clusters, but it got difficult when I had 10 or more clusters.

#### Sample output

```
--------LABEL #3---------
upper_bust MEAN: 91.98973838520523
upper_bust MEDIAN: 92.5
under_bust MEAN: 76.32611637347767
under_bust MEDIAN: 75.0
height_in MEAN: 65.91294542174109
height_in MEDIAN: 66.0
weight MEAN: 136.47812359043752
weight MEDIAN: 135.0
age MEAN: 32.1553901668922
age MEDIAN: 31.0
****Categories****
body_type
Athletic    4434
dtype: int64
pregnant
0.0    4407
1.0      27
dtype: int64
```

### Pairplot

```python
def pairplot_clusters(df, columns, label_col):
    sns.pairplot(df_body, vars=columns, hue=label_col)

```

Seaborn's pairplot can take in a hue as input, and it assigns different colors to each unique hue value. This was super useful for clustering, because now, you can see how different clusters look for each pair combination of your features.

<img src="/images/visualizing_clusters/pairplot.png">

### Category Distribution

```python
def display_category_distribution(df, cat, label, ax, val=1):
    df_cat = df[df[cat]==val].groupby(label).size()
    xrange = np.arange(df_cat.keys().shape[0])
    ax.bar(xrange, df_cat.values, align='center')
    ax.set_xticks(xrange)
    ax.set_xticklabels(df_cat.keys())
    ax.set_title('{} == {} distribution'.format(cat, val), y=1.2)
    ax.set_xlabel('cluster #')
    ax.set_ylabel('count')
```
For categorical variables, it's useful to see a barplot of the category counts in each cluster. For my particular project, this was useful to see when a model simply clusters on different categories.

<img src="/images/visualizing_clusters/barplots.png">

---
I hope this is useful for anyone of you dealing with a clustering problem!
