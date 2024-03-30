---
layout: post
title:  "Optiver stock volatility prediction"
author: penny
categories: []
image: https://newsroom.cisco.com/c/dam/r/newsroom/en/us/migrated-assets/amd-partner-innovation_800x450_thumb_031521-jpg-2146981-1-0.jpg
tags: featured
---
##### Project description

Optiver, a globally renowned high-frequency trading company, is organizing a Kaggle competition focused on discovering optimal volatility prediction models. The objective is to forecast short-term volatility for numerous stocks spanning various sectors. With access to millions of rows of detailed financial data, participants are challenged to anticipate volatility over 10-minute intervals. The performance of the models will be assessed using actual market data gathered during the three-month evaluation period following the training phase.

**Project Aim**

Predict the realized volatility for the next 10 minutes based on historical order book and transaction data for every 10-minute interval. The definition of realized volatility is as follows, where r is the log return of stock S.
$$
\sigma=\sqrt{\sum_t {r_{t-1,t}}^2}\\
r_{t-1,t}=log(\dfrac{S_{t2}}{S_{t1}})
$$
Optiver provided order book and trade data as book_train/test and train/test. The training dataset contains information from 3830 10-minute segments, which have been sampled from a total of 32767 10-minute segments across 112 stocks from 3 years historical trading data.  Sec stands for seconds_in_bucket. 

- book_train：order book data

    | time_id | sec* | bid_price1 | ask_price1 | bid_price2 | ask_price2 | bid_size1 | ask_size1 | bid_size2 | ask_size2 | stock_id |
    | ------- | ---- | ---------- | ---------- | ---------- | ---------- | --------- | --------- | --------- | --------- | -------- |
    | 5       | 0    | 1.001422   | 1.002301   | 1.00137    | 1.002353   | 3         | 226       | 2         | 100       | 0        |
    | 5       | 1    | 1.001422   | 1.002301   | 1.00137    | 1.002353   | 3         | 100       | 2         | 100       | 0        |
    | 5       | 5    | 1.001422   | 1.002301   | 1.00137    | 1.002405   | 3         | 100       | 2         | 100       | 0        |

- Trade_train: trading data

    | time_id | sec* | price    | size | order_count | stock_id |
    | :------ | :--- | :------- | :--- | :---------- | -------- |
    | 5       | 21   | 1.002301 | 326  | 12          | 0        |
    | 5       | 46   | 1.002778 | 128  | 4           | 0        |
    | 5       | 50   | 1.002818 | 55   | 1           | 0        |
    | 5       | 57   | 1.003155 | 121  | 5           | 0        |
    | 5       | 68   | 1.003646 | 4    | 1           | 0        |

- train.csv：target is y_true 

    | Stock_id | time_id | Target (σ) |
    | -------- | ------- | ---------- |
    | 9        | 5       | 0.007291   |
    | 9        | 11      | 0.002529   |
    | 9        | 16      | 0.003299   |

The main goal is to use 10 minutes of book data to predict the realized volatility of the next 10-minute window - target. When plotting bid_price1, bid_price2, ask_price1, and ask_price2, it becomes evident that throughout this 10-minute timeframe, the overarching trends of these four prices remain consistent. However, variations in price movements within specific intervals are noticeable

<p float="left">
    <img src="/Users/mumuxi/Desktop/Projects/git/02-Data-Science-Project/21-Stock Volatility Prediction/order_book_price.png"/><img src="/Users/mumuxi/Desktop/Projects/git/02-Data-Science-Project/21-Stock Volatility Prediction/order_book_price_with_trade.png"/> <em>Figure 1 (top): Order book bid and ask prices of stock id 0 time id 5.</em></p><em>Figure 2 (bottom): Added real trade price</em> 

Let's start with a basic understanding of order book first. It helps match the best available price in the market. Trades occur when a buyer's bid matches a seller's ask, leading to an agreement on the trade price.  For example, if an investor wants to purchase 20 shares of stock A, they would check the sell side of the order book to find sellers offering 221 shares at the lowest price $148. Therefore as we see in figure 2, trade price falls between bid and ask. 

![order_book_1](https://www.optiver.com/wp-content/uploads/2021/05/OrderBook3.png)



In total, there are 112 stock ids and we randomly split into train and test with 4:1 ratio. 

So the first step is to calculate the  realized volatility with the data given using formula (1). Because we have two bid prices and two ask prices, we can calculate two weighted average price wap1 and wap2. 





#### **Feature Engineering**

------

Feature engineering is quite important in this competition. 



#### Future Improvements

------





#### Conclusion

------



Reference

[1]: https://datajobs.com/data-science-repo/Recommender-Systems-Netflix.pdf	"MATRIX FACTORIZATION TECHNIQUES FOR RECOMMENDER SYSTEMS by Yehuda Koren and Chris Volinsky, published in 2009"

