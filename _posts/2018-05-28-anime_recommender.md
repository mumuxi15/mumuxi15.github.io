---
layout: post
title:  "Anime Recommender"
date:   2018-07-06 15:39:40
preview: https://www.lolacamisetas.com/3175-large_default/pokemon-tshirt-mimikyu-ghibli.jpg
---

<img style="width:100%;display:block;" src="https://i.imgur.com/1UKDz2j.jpg" />



For those who are big anime fans, our weekend mission is to find a good anime to watch. Often, we call it mission impossible! It is really hard to find a good anime you have not watched. If you search online, websites recommend animes based on popularity, new released, or genres. Such recommendations are boring and often times you have watched already because they are in the same category. What if I want a more personalized suggestion, a suggestion made by analyzing my past anime profile?  AnimeMatcher is an application built for your taste.

To get the data, I scraped 9000 anime descriptions and 5000 user profiles from MyAnimeList, a simple version of imdb for animes. Data was stored as BSON object in the MongoDB and called nime description and user watched history seperately . After combining specials and series, 4588 anime descriptions were obtained in total. 

e.g Anime description

```json
{'_id': 'Yaiba',
 'themesongs': [['"Yuuki ga Areba (勇気があれば)" by Kabuki Rocks (カブキロックス)',
   '"Shinchigakunaki Tatakai! (神智学無き戦い!)" by Kabuki Rocks (カブキロックス)']],
 'description': ["Kurogane Yaiba is a boy who doesn't want to become what any regular kid would: A samurai. That's why he undergoes a hard training with his father, knowing only the forest as his world. Then, one day, he is sent to Japan, where he has to deal with a whole new civilized reality, meeting the Mine family, the evil Onimaru and even the legendary Musashi, having lots of dangerous adventures, becoming stronger everyday.(Source: ANN, edited) "],
 'reviews': ['which are really stupid but it all succeds in tickling us!!the storycharacter and enjoyment is quite okwell i personally disliked the op and ed and art also seems quite ok {not many cute girls :( }its a lot of fun overall the series i ll definately say give 1 shot only to the 1st epi!!!ull automatically get hooked to the series atleast i did !well i hope u liked my review plz ratemy 1st reviewread more'],
 'img_url': 'https://myanimelist.cdn-dena.com/images/anime/5/71953.jpg'}
```

e.g. User watched history. Table shows anime ratings of 5 animes given by 5 users. Rating system is from 1-10.

| Anime/score     | User1 | User2 | User3 | User4 | User5 |
| --------------- | ----- | ----- | ----- | ----- | ----- |
| Fairy Tail      | 10    | 8     | 10    | 5     | ?     |
| Kimi no na wa   | 10    | 9     | ?     | ?     | 4     |
| No game no life | 9     | ?     | 9     | ?     | ?     |
| Tokyo Ghoul     | 7     | ?     | 9     | ?     | ?     |
| One Piece       | ?     | ?     | ?     | 10    | 9     |

#### How does  recommender system works ? 

------

Broadly speaking, there are two common algorithms used in recommendation system, collaborative filtering and content-Based Filtering. Collaborative filtering predicts based on past user behavior and the idea is to use opinions from other users with similar taste. Content-Based Filtering, like the name suggested, it is based on a comparison between item descriptions and a user profile.

Let’s consider converting the rating score table above to a matrix called **R<sub>rating</sub>** , this matrix holds all ratings from all users for all movies (10000 movies x 5000 users). Each column represents rating scores from a user, and we replace ? with 0.  ? means movies has not watched. 

```mathematica
[[ 10  8   10  5   0]
 [ 10  9   0   0   4]
 [ 9   0   9   0   0]
 [ 7   0   9   0   0]
 [ 0   0   0   10  9]]
```

When recommending from a large selection (which is in this case), users only have rated a few out of thousands of selections and the result will be a large sparse matrix where elements are mostly zero. Simple recommender will not be good due to sparsity problem. So how do we handle sparse matrices?

The answer is a hybrid recommender system. In case of sparsity, a hybrid approach can be more effective by combining collaborative filtering and content-based filtering. Collaborative filtering offers more interesting and diverse recommendations but suffers from cold start problem. By combining the two, It will also help to overcome the cold start problem.

**My recommender = Collaborative filtering + Content based filtering**

<img style="width:100%;display:block;" src="https://i.imgur.com/zBbWj8p.jpg">

**Recommender 1: Collaborative Filtering Workflow**

A user’s predicted rating matrix for an anime would equal the dot product of the user’s and anime’s features. Matrix Factorization formula:

 **R<sub>rating</sub> = P x Q <sup>T</sup>**

where P and Q are factor vectors that minimizes the regularized squared error. P represents the strength of the associations between users and the features. Similarly, Q represents the strength of the associations between animes and the features. Therefore, recommendations were created by calculating similarity between P and Q. 

**Recommender 2: Content-based Filtering workflow**

The key step of content-based filtering is to extract topics from text data. First step is to preprocess words with stemming and lemmatizing technique to reduce different forms of a word. For example, beautiful -> beauty, cars -> car. After that, I tried to extract topics using TF-IDF and NMF (Non-negative Matrix Factorization). TF-IDF, term frequency–inverse document frequency, is proportional to the word frequency in the document and is counteracted by the frequency of the word in the corpus. In this case, the most frequent words have less useful meaning since words like '*people*', '*place*'  occur very frequently across all documents but does not tell any information about the stories. TF-IDF is more or less a measure of how unique a word is in the corpus. 

In addition, I manually adjusted min_df and max_df parameters until succinct topics were found. We will talk more about natural language processing in other posts. In the end, 30 unique anime topics were extracted from anime story descriptions. For example, topic 7 is about solving crime and the most famous anime in that category is Detective Conan. 

Examples:

> Topic #1: Special, release, air, recap, feature  
> Topic #2: Earth, Planet, space, alien, ship  
> Topic #3: High, school, junior, student, classmate  
> Topic #4: Team, soccer, player, match, baseball  
> Topic #5: Human, race, mankind, god, survive, extinct    
> Topic #6: Magic, witch, magician, kingdom, wish  
> Topic #7: Mystery, solve, appear, past, shadow, kill  
> Topic #8: Demon, king, hero, lord, seal, defeat, mission  
> Topic #9: Love, feel, fall, relationship, confess, heart

Each anime will then have an associated probability for each topic.  For example

| Anime/Topics    | Topic 2 | Topic 3 | Topic 7 | Topic 8 | Topic 9 |
| --------------- | ------- | ------- | ------- | ------- | ------- |
| Fairy Tail      | 0.01    | 0.12    | 0.00    | 0.67    | 0.00    |
| Kimi no na wa   | 0.11    | 0.87    | 0.02    | 0.01    | 0.54    |
| No game no life | 0.35    | 0.23    | 0.34    | 0.01    | 0.35    |
| Tokyo Ghoul     | 0.02    | 0.56    | 0.76    | 0.12    | 0.00    |
| One Piece       | 0.05    | 0.00    | 0.00    | 0.45    | 0.00    |

By averaging all user's past watched anime topics, we can get user's preference on topics. Then we can calculate cosine similarity between user topic matrix and anime topic matrix to generate recommendations. 

[Website link](http://flask-env.y2vafy79mw.us-east-2.elasticbeanstalk.com) 

Then we combine the outcome from two recommender. 