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

I used AWS GPU instance (Amazon Cloud service) :

​	 **Image** : Deep Learning AMI (Ubuntu) Version 10.0 

​	 **Instance type** : p2.xlarge

Python Packages: Keras, tensorflow, opencv

#### Data

------

Let's get started with downloading following files from kaggle competition  "[Planet: Understanding the Amazon from Space](https://www.kaggle.com/c/planet-understanding-the-amazon-from-space)". 

- train-jpg.tar.7z  
- test-jpg.tar.7z 
- train_v2.csv       -   labels for the train set

<img style="width:700px;display:block;" align="left" src="https://raw.githubusercontent.com/mumuxi15/metis_proj/master/Multilabel%20image%20classification/img/eg1.jpg" />

Fig 1. Examples of labeled image chips. 

This dataset consists of 41,789 labeled and 71,000 unlabeled satellite image chips that look like above (The labels were added for readers and are separated by space) . Satellite image chips have different labels depending on its content. Each image chip consists of one unique atmospheric label and zero or multiple land use labels. 

- Weather labels: clear, cloudy, hazy, partly cloudy. 

- Land use labels
  - Commonly appeared: primary, agriculture, road, water, cultivation, habitation, bare ground
  - Rare: conventional mine,  selective logging, artisanal mine,  blooming, slash burn, blow down

*Hint: artisanal mine is another word for illegal mine*

The aim is to generate labels for any given satellite images and find those labeled artisanal mine. 

#### Build a Neural Network Model

------

I first started with a basic convolutional neural network model (CNN) with the original dataset (without any pre-process). CNN composes of multiple non-linear transformation layers and extract high-level features from data. However, even after 10 hours' training, the model failed to predict any rare land use labels, meaning the CNN model is underfit. So how can the model be improved to recognize image patterns of those with more challenging labels ? 

Therefore, I tackle the problem in two different ways: input and model structure.

- **Improve input image quality**

  Generally speaking, clearer images containing more information yield better results for Neural Network models.

- **More advanced network design**

  To learn fine details we need to increase the model complexity . Here I chose to use one of the latest Neural Network architectures, DenseNet (Dense Convolutional Network), a smarter neural network designed by [Zhuang Liu and Gao Huang](https://arxiv.org/pdf/1608.06993v3.pdf) in  2017. 

  

##### Improve image quality

I wrote a dehaze function based on  ["Single Image Haze Removal using Dark Channel Prior"](https://www.robots.ox.ac.uk/~vgg/rg/papers/hazeremoval.pdf) paper. In most cases light is scattered in the atmosphere before it reaches the camera and such scattered light is the main cause of blurry images or hazy images. To simplify, the dehaze function estimates the scattered light intensity as the maximum pixel intensity. Thus by removing the scattered light, original images can be restored.

![How hazy image is formed](https://www.researchgate.net/profile/Seung_Won_Jung2/publication/291385074/figure/fig14/AS:320880610693124@1453515307125/Formation-of-a-hazy-image.png)

Figure 2. Formation of a hazy image [$^{[1]}$](https://www.researchgate.net/profile/Seung_Won_Jung2/publication/291385074)

The dehaze function will remove the haze and increase image contrast.  Following are some examples  of before and after haze removal. As demonstrated below, it works great on both clear and hazy images.![before and after haze function](https://raw.githubusercontent.com/mumuxi15/mumuxi15.github.io/master/img/rainforest/dehaze.jpg)

Figure3. Before and after dehire function on hazy, partly cloudy and clear images.

This improves precision recall significantly especially on the rare land use labels. 

![precision](https://github.com/mumuxi15/mumuxi15.github.io/blob/master/img/rainforest/land_cover_precision.jpg?raw=true)

Figure4. Comparison of DenseNet model trained  on original images and haze free images. F2 score is a combination  of precision and recall, similar to F1 score but puts more weight on recall. Recall is more important as I would like the model to make less mistakes. 

##### DenseNet

The big difference is that DenseNet connects each layer to every other layer. Whereas traditional convolutional networks layers connect sequentially. DenseNet improved a flow of information and gradients throughout the network, therefore, it has better parameter efficiency and is quicker to train. 

I built a multi output model based on the original DenseNet code and reduced filter number and learning rate as the original model is too memory consuming. I trained the model on the labeled image sets for 4 hours and saved the model as b01_dense121.h5. Then used it to generate labels for images. 



You can check out the project in my [GitHub](https://github.com/mumuxi15/metis_proj/tree/master/Multilabel%20image%20classification)

Thanks for reading!