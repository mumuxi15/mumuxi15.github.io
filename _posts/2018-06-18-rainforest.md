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

#### Pre-process

---

Let's get started with downloading following files from kaggle competition  "[Planet: Understanding the Amazon from Space](https://www.kaggle.com/c/planet-understanding-the-amazon-from-space)". 

- train-jpg.tar.7z  
- test-jpg.tar.7z 
- train_v2.csv       -   labels for the train set

Extract images from 7z file and create two empty folder train_clean and test_clean.

```terminal
7za x train-jpg.tar.7z
tar xf train-jpg.tar
mkdir train_clean test_clean
```



The main feature engineering performed on the images is haze removal technique. It improves precision recall significantly especially on the rare land use labels.

We created a a00_remove_haze.py function based on  ["Single Image Haze Removal using Dark Channel Prior"](https://www.robots.ox.ac.uk/~vgg/rg/papers/hazeremoval.pdf) paper and increased the contrast after.



![How hazy image is formed](https://www.researchgate.net/profile/Seung_Won_Jung2/publication/291385074/figure/fig14/AS:320880610693124@1453515307125/Formation-of-a-hazy-image.png)

In most cases light is scattered in the atmosphere before it reaches the camera and the scattered light is the main cause of blurry images or hazy images. The a00_remove_haze.py haze removal function estimates the scattered light intensity as the maximum pixel intensity. Thus image is restored by removing the scattered light. Following are some examples  of before and after haze removal function. As demonstrated below, it works great on both images labelled clear and hazy. 

![dehaze](https://github.com/mumuxi15/mumuxi15.github.io/blob/master/img/dehaze.jpg?raw=true)















{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')

{% endhighlight %}

[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
