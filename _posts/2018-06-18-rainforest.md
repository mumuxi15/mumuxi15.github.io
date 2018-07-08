---
layout: post
title:  "Amazon Illegal Mining Detection"
date:   2018-06-18 15:07:19
categories: [machine learning]
comments: true
image:
  feature: https://process.filestackapi.com/AhTgLagciQByzXpFGRI0Az/crop=dim:[0,0,1250,260]/https://cdn1.img.sputniknews.com/images/105730/27/1057302722.jpg
---
Amazon deforestation has been a serious concern over the past decades due to its devastating impact on biodiversity, habitat lost and climate change. One of the cause is the illegal gold mining. 

To pretect the beautiful Amazon forest from illegal mining, we can use satellite images combined with machine learning techniques to detect locations of such illegal activities. 

<!--more-->

#### System Requirement

------

I used Amazon’s EC2 GPU instance:

​	image: Deep Learning AMI (Ubuntu) Version 10.0 

​	instance type: p2.xlarge

#### Data

------

Let's get started with downloading following files from kaggle competition  "[Planet: Understanding the Amazon from Space](https://www.kaggle.com/c/planet-understanding-the-amazon-from-space)". 

- train-jpg.tar.7z  
- test-jpg.tar.7z 
- train_v2.csv       -   labels for the train set

Extract jpg from 7z file and create two empty folder train_clean and test_clean.

{% highlight linux %}

7za x train-jpg.tar.7z
tar xf train-jpg.tar
mkdir train_clean test_clean

{% endhighlight %}

#### **Insert examples here ** 

#### Pre-process

---

{% highlight python3 %}

python3 a00_remove_haze.py

{% endhighlight %}

The next step is run a00_remove_haze.py, the dehaze function will remove the haze, clear the images and increase image contrast. This improves precision recall significantly especially on the land use labels. The dehaze function is based on  ["Single Image Haze Removal using Dark Channel Prior"](https://www.robots.ox.ac.uk/~vgg/rg/papers/hazeremoval.pdf) paper.

![How hazy image is formed](https://www.researchgate.net/profile/Seung_Won_Jung2/publication/291385074/figure/fig14/AS:320880610693124@1453515307125/Formation-of-a-hazy-image.png)

In most cases light is scattered in the atmosphere before it reaches the camera and such scattered light is the main cause of blurry images or hazy images. a00_remove_haze.py  function estimates th scattered light intensity as the maximum pixel intensity. Thus by removing the scattered light images can be restored. Following are some examples  of before and after haze removal function. As demonstrated below, it works great on both images labelled clear and hazy.

![How hazy image is formed](https://raw.githubusercontent.com/mumuxi15/mumuxi15.github.io/master/img/dehaze.jpg)

#### Build a Neural Network Model

------

I first started with a basic convolutional neural network model (CNN) with the original dataset (without the haze removed). Even after 100 runs, the models failed to predict any rare land use labels, meaning the CNN model is underfit. So how can the model be improved to recognize image patterns of those with more challenging labels ? 

Therefore, I attackle the problem in two different ways: input and model structure.

- Improve input images' quality. Generally speaking, clearer images containing more informations and might yield better results for Neural Network models.
- More advanced network design. To learn fine details we need to increase the model complexity . Here I chose to use one of the best performing model, Dense Convolutional Network (DenseNet), a smarter neural network design by [Zhuang Liu and Gao Huang](https://arxiv.org/pdf/1608.06993v3.pdf). 

The big difference is that DenseNet connects each layer to every other layer in a feed-forward fashion. Whereas traditional convolutional networks layers connets in subsequential orders. Other than that, DensetNet has better parameter efficiency, quicker to train. 



I implemented the DenseNet code from [flyyufelix's github](https://github.com/flyyufelix/DenseNet-Keras/blob/master/densenet121.py) and made it simpler and less memory consuming.



{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')

{% endhighlight %}

[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
