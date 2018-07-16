---
layout: post
title:  "Anime Recommender System"
date:   2018-05-28 09:05:10
categories: [Recommender]
comments: true
image:
  feature: https://process.filestackapi.com/AhTgLagciQByzXpFGRI0Az/resize=width:1024/https://i.imgur.com/1UKDz2j.jpg
---
If you google "ran out of anime to watch", there are 28 million related search results. As a big anime fan myself, I always struggling to find new animes to watch. On the market, a lot anime websites recommend animes based on popularity, new released, or genres you most frequently visited. What if I want more personalized and more about the story?  AnimeMatcher is an application built based on your watched history and anime stories.

<!--more-->

#### System Requirement

------

Database: MongoDB

Python packages: Scrapy, Pymongo, nltk

#### Data

------

Scraped 9000 anime descriptions and 5000 user profiles from myAnimeList using Scrapy and stored to MongoDB. After combining specials and different series, there are 4588 animes descriptions. To extract better topics, descriptions were broken into sentences.

User datebase contains user name, watched anime IDs, titles and rating score. Examples for user data.

```json
{'_id': ObjectId('5b085ac6b567e3749a9e84b8'),
 'user': 'Akin_AI',
 'anime_id': [6702,22043,12049,5114,32281,15315,23755,19815,30296,22319,27899],
 'anime_title': ['Fairy Tail',
  'Fairy Tail (2014)',
  'Fairy Tail Movie 1: Houou no Miko',
  'Fullmetal Alchemist: Brotherhood',
  'Kimi no Na wa.',
  'Mondaiji-tachi ga Isekai kara Kuru Sou Desu yo?',
  'Nanatsu no Taizai',
  'No Game No Life',
  'Rakudai Kishi no Cavalry',
  'Tokyo Ghoul',
  'Tokyo Ghoul √A'],
 'anime_score': [10, 10, 8, 9, 9, 8, 9, 8, 8, 8, 8]}
```

Examples for animes

```json
{'_id': 'Yaiba',
 'themesongs': [['"Yuuki ga Areba (勇気があれば)" by Kabuki Rocks (カブキロックス)',
   '"Shinchigakunaki Tatakai! (神智学無き戦い!)" by Kabuki Rocks (カブキロックス)']],
 'description': ["Kurogane Yaiba is a boy who doesn't want to become what any regular kid would: A samurai. That's why he undergoes a hard training with his father, knowing only the forest as his world. Then, one day, he is sent to Japan, where he has to deal with a whole new civilized reality, meeting the Mine family, the evil Onimaru and even the legendary Musashi, having lots of dangerous adventures, becoming stronger everyday.(Source: ANN, edited) "],
 'reviews': ['which are really stupid but it all succeds in tickling us!!the storycharacter and enjoyment is quite okwell i personally disliked the op and ed and art also seems quite ok {not many cute girls :( }its a lot of fun overall the series i ll definately say give 1 shot only to the 1st epi!!!ull automatically get hooked to the series atleast i did !well i hope u liked my review plz ratemy 1st reviewread more'],
 'img_url': 'https://myanimelist.cdn-dena.com/images/anime/5/71953.jpg'}
```



#### Content based recommender systems

Content based system recommend items xxxxx

- Download nltk libraries

  ```python
  import nltk
  nltk.download('punkt')
  nltk.download('wordnet')
  nltk.download('averaged_perceptron_tagger')
  ```

- Break into bags of words

  - Break words to tokens
  - Reduce different forms of a word to a common base form using stemming and lemmatizing 

- Topic Modelling 

  - Feature_extraction using TF-IDF
  - Decomposition techniques using NMF

**TF-IDF (Term Frequency–Inverse Document Frequency**)

A tf-idf transformer is applied to the bag of words matrix that NMF must process with the TfidfVectorizer.

#### Collaborative filtering

Collaborative filtering is based on past interactions between users and ratings. 

TF-IDF (Term Frequency–Inverse Document Frequency)









<img src="https://i.imgur.com/zBbWj8p.jpg">

**Improve input image quality**

Generally speaking, clearer images containing more information yield better results for Neural Network models.

- **More advanced network design**

  Increased model complexity is necessary to learn finer details. Here I chose to use one of the latest Neural Network architectures: DenseNet (Dense Convolutional Network), a smarter neural network designed by [Zhuang Liu and Gao Huang](https://arxiv.org/pdf/1608.06993v3.pdf) in 2017.  

  

##### Improve image quality

 I wrote a dehaze function based on the paper: ["Single Image Haze Removal using Dark Channel Prior"](https://www.robots.ox.ac.uk/~vgg/rg/papers/hazeremoval.pdf). In most cases, light is scattered in the atmosphere before it reaches the camera. Such scattered light is the main cause of blurry images or hazy images. To simplify, the dehaze function estimates the scattered light intensity as the maximum pixel intensity. Thus by removing the scattered light, original images can be restored.   



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