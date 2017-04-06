---
layout: post
title: Transferring Style with Style Transfer
tags: styletransfer deeplearning
author: Josephine Tirtanata
---

For my project Beauty Bot, I created a proof of concept model using style transfer.

## Motivations
I wanted to create a model that can preview how a certain make up will look on you. It is possible to train a CNN to preview how a certain make up product will look on you if you have enough before and after pictures. However getting clean training pictures was difficult. There's almost always more than one product applied onto the face, and also, a change in the hair style, facial expression, or background on the after picture. On top of that, it was impossible to map it to the product or products they were using. I was almost ready to give up on this idea, when my teacher Julia told me to look into style transfer.

## Style Transfer Concept
To create a style transfer image, you give it two input images; a **base image** and a **style image**. The resulting output will minimize the difference of *content* with the base image and the difference in *style* with the style image. Below is an example of a style transfer. These losses can be calculated at different layers in the VGG16 architecture (if you're not sure what VGG16 is, read about it [here](https://blog.keras.io/how-convolutional-neural-networks-see-the-world.html)). As you will see later, selecting these layers can have a profound impact.

<img src="/images/styletransfer/example.jpg">

The difference of content sounds simple, you could probably calculate the difference in the two images pixel by pixel, right? And that's actually what happens. However, using the term pixel here is wrong, because what we're putting into the calculation is the image representation matrix in a certain layer. However, how does one calculate style loss? How do you capture style, let alone calculate the differences of styles between two pictures?

The original style transfer [paper](http://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Gatys_Image_Style_Transfer_CVPR_2016_paper.pdf) introduces the **gram matrix**, which is the image multiplied by a transverse of itself. Gatys, the author of this paper, described this "captures texture information but not global information". The way I understand it is, when you have a matrix multiplied with a transverse of itself, you have the multiplication of all of the combinations of the elements in the matrix. Hence, you forgo the spatial information of the image, and get the relationship of the elements in the image representation.

Aside from these loss functions, there's another one that is not in the original paper but was introduced later on by others because it was shown to improve the beauty of the output image. This is referred as the *total variation loss* and it encourages the overall spatial smoothness of the image.

## Implementation
I used `keras` with `tensorflow` as backend to implement the style transfer. Thanks to Kevin's github [repo](https://github.com/kevinzakka/style_transfer), I was able to have the basic set up of the model. Then, I focus on figuring out which layers I am going to calculate the style and content loss in.

In the VGG16 network, there's, as the name suggests, 16 layers. Within these layers, there are several convolutional layers to calculate the loss functions in. The intuition is; the deeper the layer, the more high-level the image is represented. Compare the two images below.

<img src="/images/styletransfer/first.png">

<img src="/images/styletransfer/second.png">

In the first image, I made the mistake of calculating the content loss at the 4th block of the VGG16 network. This is pretty deep into the architecture (there's 5 blocks of convolutional layers in total), and this a visual example that shows what happens when you calculate the difference in content at a high-level. Essentially, it encourages the eyes in the input and content image to be similar, without caring too much about the spatial information of the eyes. Same goes with the eyebrows, nose, and lips. Realizing this, I calculated the content loss at the second block, and that is the second picture. There's certainly work to do with the style image, but I am satisfied with the way the spatial information and structure of the base image is kept in the output image.

What became obvious with this second picture, is the fact that the eyelashes were considered a "style" to be applied in the output image. Understanding this, the style image that would be suitable for this is one that doesn't have any defined structure of its own. Combining a solid blue image with the eye, this is what I get -

<img src="/images/styletransfer/blue.png">

I found that the best results is when I blur the surrounding eyeshadow and provide a skin tone background. The image below shows the particular eyeshadow transformation that I have chosen.


<img src="/images/styletransfer/eyeshadow.png">

Then, I experimented the different layers that I can calculate the style loss in.

<img src="/images/styletransfer/stylelayers.png">

Calculating the style loss at blocks 1 - 4 (upper left image), results in  the glittered grey texture in every structure of content in the base image. Even though the structure of the eye and the creases of the eyelids are still defined, the glittered gray texture is applied heavily throughout. Moving to just blocks 3 and 4 (bottom left image), the grey is less defined, but what strikes me is the fact that the glitter is so defined. At a higher level, I can see that the silvery balls that essentially makes glitter looks like glitter, is heavily emphasized.

I achieved better results when I select the earlier layers (images on the left). Even though these two are similar, the one at the first block gives the best result (bottom left image) because what happens is; there's more weight on the eye remaining an eye, hence less glitter effect happens on the sclera and iris.

<img src="/images/styletransfer/results.png">

I put the styled eyes back to the original image and was really pleased with the results. The eyes are slightly lighter, which I assume is due to gray being a common color for an eye, while the white of the sclera remains white because VGG16 knows it to only have a white color. Despite the discoloration, style transfer is very capable of transferring the color and texture of one image to another. I can see that the grey eyeshadow looks bluish in this person's complexion, which pretty much is the goal!
