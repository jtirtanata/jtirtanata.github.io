---
layout: post
title: Autoencoder Recommendation Model
tags: autoencoder neuralnetworks deeplearning recommendation
author: Josephine Tirtanata
---

For my project Beauty Bot, I created a recommendation model that was inspired
from an autoencoder model.

## Basic Concept
Autoencoder is a neural network typically used to clean out noisy images. There's actually nothing very "deep" about it as it has only one hidden layer. The input and output layer is of the same dimension, while the hidden layer has a much smaller dimension.

<img src="/images/autoencoder/architecture.png">

The objective is to have the output as close to the input layer as possible with going through the bottleneck layer. You can see how such a model can work to clean noisy images as the random noise will likely be discarded from the output image while stronger patterns will be kept.

As of late, there's a lot of scholarly articles talking about using autoencoders in collaborative filtering (one of them [here](http://users.cecs.anu.edu.au/~u5098633/papers/www15.pdf)). The idea is that the bottleneck layer will "sum up" user's preferences and predict ratings on other items that have not been rated yet. I was very intrigued with this idea, and decided to implement it myself.

## Implementation

### Data
The data I gathered consists of around 800k ratings for 800 products in Sephora, with around 500k unique users.

### Preprocessing
The data is originally a rating from one to five, however I simply bucketed 1 - 3 stars as a thumbs down, and 4 - 5 as a thumbs up. I do believe that a five star rating is no longer the best way to capture how strongly a user likes a product, as it is difficult to distinguish a 1 from a 2 rating, or a 4 from a 5. I simply wanted to capture the products that users like, and would recommend for others to buy. I recently found that [Netflix](http://www.theverge.com/2017/3/16/14952434/netflix-five-star-ratings-going-away-thumbs-up-down) feels the same way.

Ultimately, I had a 800 long vector for each input, representing products that users have liked and disliked. I gave a score of 1 to a rating of 4 - 5, and 0.1 for 1 - 3. I reserved 0 for products that have not been tried yet.

### Tools and libraries

```python
from keras.models import Sequential, Model
from keras.layers import Dense, Activation, Input, Merge, Dropout
from keras.layers.normalization import BatchNormalization
from numpy.random import seed
import theano
import theano.tensor as T
```
I used `keras` and `theano` along with CUDA on a GPU instance in AWS.

### Code

I had to create a custom loss function. For the autoencoder to work, we don't want the error to increase if the model predicted a value for a product that's not rated yet.

```python
def loss_func(y_true, y_pred):
    temp = np.dot(y_true, y_pred)
    temp_true = y_true.nonzero_values()
    temp_pred = temp.nonzero_values()
    temp_pred = temp_pred / temp_true
    loss = T.mean((temp_pred - temp_true)**2)
    return loss
```

`keras` made the rest of the process easy. Simply create a model with 3 layers with the input and output dimensions the size of the products that is available.

```python
branch1 = Sequential()
branch1.add(Dense(user_encoding_dim, input_shape = (user_dim,), init = 'normal', activation = 'relu'))
branch1.add(Dropout(0.2))

branch2 = Sequential()
branch2.add(Dense(encoding_dim, input_shape =(rating_dim,), init = 'normal', activation = 'relu'))
branch1.add(BatchNormalization())
branch2.add(Dropout(0.2))

model = Sequential()
model.add(Merge([branch1, branch2], mode = 'concat'))
model.add(Dense(rating_dim, init = 'normal', activation = 'sigmoid'))
model.compile(loss = loss_func, optimizer = 'adadelta')
seed(2017)
```

To clarify what's happening here, I added a branch that represents user's information incase there are patterns between that and their preferences.

### Results

After a night of training, I achieved a training MSE of training error: 0.0196!

<img src="/images/autoencoder/losses.png">

It's amazing how good the performance is despite it having such a simple architecture! 
