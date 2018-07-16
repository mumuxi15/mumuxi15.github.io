---
layout: post
title:  "Amazon Illegal Mining Detection"
date:   2018-06-18 15:07:19
categories: [machine learning]
comments: true
image:
  feature: https://process.filestackapi.com/AhTgLagciQByzXpFGRI0Az/crop=dim:[0,0,1250,260]/https://cdn1.img.sputniknews.com/images/105730/27/1057302722.jpg
---
Amazon deforestation has been a serious concern over the past several decades due to its devastating impact on biodiversity, habitat lost and climate change. One of the causes is illegal gold mining. To protect the beautiful Amazon forest, we can combine satellite images with machine learning techniques to keep track of deforestation and detect such illegal activities. 

<!--more-->

#### System Requirement

------

I used AWS GPU instance (Amazon Web Service) : 

- Image: Deep Learning AMI (Ubuntu) Version 10.0 
- Instance type: p2.xlarge 
- Python Packages: Keras 2.1.6, Tensorflow, Opencv 

#### Data

------

Let's get started with downloading following files from Kaggle  "[Planet: Understanding the Amazon from Space](https://www.kaggle.com/c/planet-understanding-the-amazon-from-space)". 

- train-jpg.tar.7z  
- test-jpg.tar.7z 
- train_v2.csv       -   labels for the train set

<img style="width:500px; display:inline-block;" src="https://raw.githubusercontent.com/mumuxi15/metis_proj/master/Multilabel%20image%20classification/img/eg1.jpg" alt="image chips examples" />

Figure 1. Examples of labeled image chips

This dataset consists of 41,789 labeled and 71,000 unlabeled satellite image chips that look like above (the labels were added for readers and are separated by commas). Image chips have different labels depending on their content. Each image chip consists of one atmospheric label and multiple land use labels.

- Weather

  clear, cloudy, hazy, partly cloudy

- Land use
  - Commonly appeared: primary, agriculture, road, water, cultivation, habitation, bare ground
  - Rare: conventional mine,  selective logging, artisanal mine,  blooming, slash burn, blow down

*Note: artisanal mine is another word for illegal mine* 

The aim is to generate labels for any given satellite image and to identify illegal mines.  

#### Build a Neural Network Model

------

I first started with a basic convolutional neural network model (CNN) with the original dataset (without any pre-processing). CNN comprises multiple non-linear transformation layers and extracts high-level features from data. However, even after 10 hours of training, the model failed to predict any rare land use labels, meaning the CNN model is underfit. The challenge then posed: how could the model be improved to classify more challenging labels? 

Therefore, I tackled the problem in two different ways: input and model structure.

- **Improve input image quality**

  Generally speaking, clearer images containing more information yield better results for Neural Network models.

- **More advanced network design**

  Increased model complexity is necessary to learn finer details. Here I chose to use one of the latest Neural Network architectures: DenseNet (Dense Convolutional Network), a smarter neural network designed by [Zhuang Liu and Gao Huang](https://arxiv.org/pdf/1608.06993v3.pdf) in 2017.  

##### Improve image quality

 I wrote a dehaze function based on the paper: ["Single Image Haze Removal using Dark Channel Prior"](https://www.robots.ox.ac.uk/~vgg/rg/papers/hazeremoval.pdf). In most cases, light is scattered in the atmosphere before it reaches the camera. Such scattered light is the main cause of blurry images or hazy images. To simplify, the dehaze function estimates the scattered light intensity as the maximum pixel intensity. Thus by removing the scattered light, original images can be restored.    

<img style="width:550px; display:inline-block;" src="https://www.researchgate.net/profile/Seung_Won_Jung2/publication/291385074/figure/fig14/AS:320880610693124@1453515307125/Formation-of-a-hazy-image.png" />



 Figure 2. Formation of a hazy image. Camera = Direct attenuation+Airlight, where t(x) is the transmission coefficient

 The resulting effect of using the dehaze function is the removal of haze and an increase in image contrast.  Below are some examples of before and after haze removal. As demonstrated, it works great on both clear and hazy images. 

<img style="width:600px;display:inline-block;" src="https://raw.githubusercontent.com/mumuxi15/mumuxi15.github.io/master/img/rainforest/dehaze.jpg" />

Figure3. Before and after dehaze function on hazy, partly cloudy and clear images

This improves both precision and recall significantly especially on the rare land use labels. F2 score is a combination of precision and recall, similar to F1 score but puts more weight on recall. Recall is more important as I would like the model to make less mistakes.  

<img style="width:600px;display:inline-block;" src="https://github.com/mumuxi15/mumuxi15.github.io/blob/master/img/rainforest/land_cover_precision.jpg?raw=true" />

Figure 4. Comparison of DenseNet model trained on original images and haze free images

##### DenseNet

The big difference is that DenseNet connects each layer to every other layer whereas traditional convolutional network layers connect sequentially. DenseNet improves the flow of information and gradients throughout the network, therefore it has better parameter efficiency resulting in a faster training time. 

I built a multi-output model based on the original DenseNet code and reduced the filter number and learning rate as the original model is too memory consuming for p2.xlarge (cloud service). I trained the model on the labeled image sets for 4 hours and saved the model as b01_dense121.h5. Then used it to generate labels for images. 

You can check out the project in my [GitHub](https://github.com/mumuxi15/metis_proj/tree/master/Multilabel%20image%20classification)

Thanks for reading !