---
layout: post
title:  "Avocado Price Prediction"
date:   2018-09-10 15:07:19
preview: https://i.imgur.com/JPMdeMx.jpg
---
Intro: Avocado, as US's most popular fruits and best selling..xx.x..x.x.x. US consumes xx kg per ..x..x.

Price of avocado is cheapter xx (winter/summer)



The dataset is scraped from [Hass Avocado Board](http://www.hassavocadoboard.com/retail/volume-and-price-data) from 2007-2018. 

1. Clean, sort in time order and check if null exists.
2. 









- Image: Deep Learning AMI (Ubuntu) Version 10.0 
- Instance type: p2.xlarge 
- Python Packages: Keras 2.1.6, Tensorflow, Opencv 

Examples of some image chips (the labels were added for readers). Each image chip consists of multiple labels and are separated by commas, such as clear, cloudy, hazy, road, water, cultivation,artisanal mine and blooming. The dataset is quite challenging as labels are highly imbalanced with a few labelling error.  Red, blue, purple indicates <span style="color:SALMON"> atmospheric conditions</span>,  <span style="color:LightSkyBlue"> common land uses</span> and  <span style="color:Plum"> rare land uses</span>.

*Note: artisanal mine is another word for illegal mine*

<p float="left">

<img src="https://i.imgur.com/GsW5QR2.jpg" width="50%" />  <img src="https://lh3.googleusercontent.com/jn0yWdVFz-RplTsir-DZcRs0UYWSouwjwhknKi3J6-f-o4TPWBlL2AGNsKQa0NIBkPJ66XfUfKrB03-BmHo8vDq2dJhf6lZLRuhQmluBukP2V979NtW7NZ-5odX8mhEru029s6PDy40" width="49%" />
 </p>

#### Build a Neural Network Model

------

First I started with a basic convolutional neural network model (CNN). CNN comprises multiple non-linear transformation layers and extracts high-level features from images. It basically trains model to find the best set of weights for maximizing a neural network’s accuracy. Although I had 70,000 clearly labeled training images,  results were not as good as expected. After 10 hours of training, the CNN model failed to predict any rare land use labels, meaning it is underfit. The challenge then posed: how could the model be improved to classify minority class? 

Two approaches I made to solve the problem:

<ul>

<li>Improve input image quality</li>

<li>More advanced network designs</li>

</ul>

##### **Improve image quality**

Generally speaking, clearer images contains more information yield better results for neural networks. Since many details were covered under the haze, I wrote a dehaze function based on a paper: ["Single Image Haze Removal using Dark Channel Prior"](https://www.robots.ox.ac.uk/~vgg/rg/papers/hazeremoval.pdf). In most cases, light is scattered in the atmosphere before it reaches the camera and such scattered light is the main cause of blurry images or hazy images. To simplify, we estimates the scattered light intensity as a constant approximates to the maximum pixel intensity of the darkest RGB channel. Thus images can be restored, by subtracting the haze constant from photo intensity.

<img style="width:80%;display:block;" src="https://www.researchgate.net/profile/Seung_Won_Jung2/publication/291385074/figure/fig14/AS:320880610693124@1453515307125/Formation-of-a-hazy-image.png" />



 Figure 2. Formation of a hazy image. Camera = Direct attenuation+Airlight

The resulting effect of using the dehaze function is the removal of haze and an increase in image contrast.  Below are some examples of before and after haze removal. As demonstrated, it works great on both clear and hazy images. 

<img style="width:80%;display:inline-block;" src="https://lh5.googleusercontent.com/kLzNKsQnGef5RmNFomjswAF6Fx37KHPC4mEP4zfHRfQZAKSUzEL-nEVMziqYLKAMHZ07v8vWbgYhfntR3l7KGLRKXKhAIaFDLrh20OsqbK6L_U7wHFfsR6JPf5-WwMKq4ToEtGNwHrM"/>

Figure3. Before and after applying dehaze function on hazy, partly cloudy and clear images

This shows significant improvement on both precision and recall, especially the rare land use case. As both metrics are important and fewer false negatives are preferred, F2 score is used to evaluate the model. It is a combination of precision and recall and it puts more weight on recall.

<img style="width:80%;display:inline-block;" src="https://lh6.googleusercontent.com/P4qLeEVjt-Xh1vPbRrR12i6W43sfm03gZnA5x4NAoSkD4rkqx5cYPlmu9EplmXZDWM0TDudJzw-OOGQIOJ26T4VAFf2sD6isNkzWEyyZJOosXJpH5xXg581AVMpYm1B8j007y6BbdXk" />

Figure 4. Comparison of DenseNet model trained on original images and haze free image

#### Model Performance

------

I trained the Dense Net model on the hazed removed image sets for 4 hours and saved the model as b01_dense121.h5. Then used it to generate labels for unlabelled test set images. Here are some examples of test photo chips marked as illegal mines. 

<img style="width:80%;display:inline-block;" src="https://imgur.com/LSL0RMq.png"/>



The averaged precision and recall for all labels is 0.89 and 0.81. Out of 90,000 test images, 78 illegal mine photos were successfully detected, which gives precision of 0.69 and recall of 0.45. 

Project in posted in my [GitHub](https://github.com/mumuxi15/metis_proj/tree/master/Multilabel%20image%20classification). Thanks for reading !