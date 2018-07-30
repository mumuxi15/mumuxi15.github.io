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

9000 anime descriptions and 5000 user profiles were scraped from myAnimeList using Scrapy and stored directly as BSON object in the MongoDB. After combining specials and different series, there are 4588 animes descriptions. 

- Anime database consists of themesongs, image links, descriptions and review. Examples for animes

```json
{'_id': 'Yaiba',
 'themesongs': [['"Yuuki ga Areba (勇気があれば)" by Kabuki Rocks (カブキロックス)',
   '"Shinchigakunaki Tatakai! (神智学無き戦い!)" by Kabuki Rocks (カブキロックス)']],
 'description': ["Kurogane Yaiba is a boy who doesn't want to become what any regular kid would: A samurai. That's why he undergoes a hard training with his father, knowing only the forest as his world. Then, one day, he is sent to Japan, where he has to deal with a whole new civilized reality, meeting the Mine family, the evil Onimaru and even the legendary Musashi, having lots of dangerous adventures, becoming stronger everyday.(Source: ANN, edited) "],
 'reviews': ['which are really stupid but it all succeds in tickling us!!the storycharacter and enjoyment is quite okwell i personally disliked the op and ed and art also seems quite ok {not many cute girls :( }its a lot of fun overall the series i ll definately say give 1 shot only to the 1st epi!!!ull automatically get hooked to the series atleast i did !well i hope u liked my review plz ratemy 1st reviewread more'],
 'img_url': 'https://myanimelist.cdn-dena.com/images/anime/5/71953.jpg'}
```

Here is an example diagram of anime ratings by different users. Rating system is from 1-10

Table1. User anime ratings

| Anime/score     | User1 | User2 | User3 |
| --------------- | ----- | ----- | ----- |
| Fairy Tail      | 10    | 8     | 10    |
| Kimi no na wa   | 10    | 9     | ?     |
| No game no life | 9     | ?     | 9     |
| Tokyo Ghoul     | 7     | ?     | 9     |
| One Piece       | ?     | ?     | 9     |

#### Build a hybrid recommender system

------

Here’s what the ratings matrix might look like. Each column represents rating scores from a user. When recommending from a large selection, users will have reated only a few and the result will be a sparse matrix where most elements are zero. First, let’s convert Table 1 to a 10000 X 5000 matrix called **$M_{rating}$**, this matrix holds all ratings from all users for all movies (10000 movies x 5000 users).

```mathematica
[[ 10  5   0   0   0   9]
 [ 0   0   8   10  0   4]
 [ 8   10  0   0   6   0]
 [ 0   10  9   0   10  10]
 [ 1   0   0   0   5   0]
 [ 0   0   0   0   4   0]]
```

The most simple way of user preference prediction is to calculate cosine similarity, however, in this case, results will not be good due to sparsity of $M_{rating}$. So how to handel large and sparse matrices ?

The answer is a hybrid recommender system. Collaborative filtering predicts based on past user behavior and the idea is to use opinions from other users with similar taste. However, it can only predict ratings if there are enough users who have rated it. In case of sparsity, a hybrid approach can be more effetive by combining collaborative filtering and content-based filtering.

**Hybrid = Collaborative filtering + Content based filtering**



Steps

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

TF-IDF (Term Frequency–Inverse Document Frequency)









<img src="https://i.imgur.com/zBbWj8p.jpg">

Please wait for the update, .... 