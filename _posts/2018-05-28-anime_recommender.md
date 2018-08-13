---
layout: post
title:  "Anime Recommender System"
date:   2018-05-28 09:05:10
categories: [Recommender]
comments: true
image:
  feature: https://process.filestackapi.com/AhTgLagciQByzXpFGRI0Az/resize=width:1024/https://i.imgur.com/1UKDz2j.jpg
---
On a casual Saturday night with friends, a problem we always facing is we ran out of anime to watch ! If you seach on google, there are millions of people with similar concerns. Anime Crisis ! 

On Internet, many websites recommend animes based on popularity, new released, or genres. What if I want a more personalized suggestion and concentrate more about the story?  AnimeMatcher is an application built based on your watched history and anime stories.

<!--more-->

#### Data

------

I scraped 9000 anime descriptions and 5000 user profiles from myAnimeList and stored as BSON object in the MongoDB. After combining specials and series, I obtained 4588 animes descriptions in total. Data was stored as anime description and user watched history seperately in MongoDB . 

e.g Anime description

```json
{'_id': 'Yaiba',
 'themesongs': [['"Yuuki ga Areba (勇気があれば)" by Kabuki Rocks (カブキロックス)',
   '"Shinchigakunaki Tatakai! (神智学無き戦い!)" by Kabuki Rocks (カブキロックス)']],
 'description': ["Kurogane Yaiba is a boy who doesn't want to become what any regular kid would: A samurai. That's why he undergoes a hard training with his father, knowing only the forest as his world. Then, one day, he is sent to Japan, where he has to deal with a whole new civilized reality, meeting the Mine family, the evil Onimaru and even the legendary Musashi, having lots of dangerous adventures, becoming stronger everyday.(Source: ANN, edited) "],
 'reviews': ['which are really stupid but it all succeds in tickling us!!the storycharacter and enjoyment is quite okwell i personally disliked the op and ed and art also seems quite ok {not many cute girls :( }its a lot of fun overall the series i ll definately say give 1 shot only to the 1st epi!!!ull automatically get hooked to the series atleast i did !well i hope u liked my review plz ratemy 1st reviewread more'],
 'img_url': 'https://myanimelist.cdn-dena.com/images/anime/5/71953.jpg'}
```

e.g. User watched history. Table shows anime ratings of 5 animes given by 5 different users. Rating system is from 1-10.

| Anime/score     | User1 | User2 | User3 | User4 | User5 |
| --------------- | ----- | ----- | ----- | ----- | ----- |
| Fairy Tail      | 10    | 8     | 10    | 5     | ?     |
| Kimi no na wa   | 10    | 9     | ?     | ?     | 4     |
| No game no life | 9     | ?     | 9     | ?     | ?     |
| Tokyo Ghoul     | 7     | ?     | 9     | ?     | ?     |
| One Piece       | ?     | ?     | ?     | 10    | 9     |

#### Build a hybrid recommender system

------

Let’s convert Table 1 to a 10000 X 5000 matrix called **M<sub>rating</sub>** , this matrix holds all ratings from all users for all movies (10000 movies x 5000 users). Each column represents rating scores from a user. When recommending from a large selection, users will have reated only a few and the result will be a sparse matrix where most elements are zero.

```mathematica
[[ 10  8   10  5   0]
 [ 10  9   0   0   4]
 [ 9   0   9   0   0]
 [ 7   0   9   0   0]
 [ 0   0   0   10  9]]
```

The most simple way of user preference prediction is to calculate cosine similarity, however, in this case, results will not be good due to sparsity of **M<sub>rating</sub>** . So how to handel large and sparse matrices ?

The answer is a hybrid recommender system. Collaborative filtering predicts based on past user behavior and the idea is to use opinions from other users with similar taste. However, it can only predict ratings if there are enough users who have rated it. In case of sparsity, a hybrid approach can be more effetive by combining collaborative filtering and content-based filtering. It also helps to overcome common problems of recommenders such as cold start and the sparsity problem.

**My Hybrid recommender = Collaborative filtering + Content based filtering**

Steps









1. Break into bags of words

- Break words to tokens
- Reduce different forms of a word to a common base form using stemming and lemmatizing 

- Topic Modelling 

  - Feature_extraction using TF-IDF
  - Decomposition techniques using NMF

**TF-IDF (Term Frequency–Inverse Document Frequency**)

A tf-idf transformer is applied to the bag of words matrix that NMF must process with the TfidfVectorizer.

TF-IDF (Term Frequency–Inverse Document Frequency)





NLP stands for Natural Language Processing, it is defined as the computational technique to understand and manipulate human language and speech. Here we are going to demostrate how we can extract topics from anime descriptions with help of NLP.

Challenges of NLP:

- 



<img src="https://i.imgur.com/zBbWj8p.jpg">

Please wait for the update, .... 



Conclusion:

The important factors of a good recommender: diversity, privacy, robustness, serendipity, labelling.