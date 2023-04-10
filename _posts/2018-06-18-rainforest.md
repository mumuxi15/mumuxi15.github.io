---
layout: post
title:  "Amazon Illegal Mining Detection"
date:   2018-06-18 15:07:19
preview: https://live.staticflickr.com/65535/49721870336_ddbfcaa96b.jpg
---
Amazon deforestation has been a major concern due to its negative impact on biodiversity, habitat destruction, and climate change. Over the past decade, the increasing global gold price has led to a rise in illegal gold mining activities within the region. To detect land use changes associated with illegal mining, satellite images can be analyzed to locate the illegal mines using machine learning technique. 



The dataset used for this project is from "[Planet: Understanding the Amazon from Space](https://www.kaggle.com/c/planet-understanding-the-amazon-from-space)", where satellite images were divided into small chips and randomly assigned numbers as names. The goal of the project is to generate labels that best describe the content of the image and thus identify those that are marked as illegal mines. To accomplish this, I used an AWS GPU instance (Amazon Web Service), as complex neural networks often require better hardware than standard PC setups. The instance used was a default GPU instance priced at approximately $0.772 per hour and used the following:

- Image: Deep Learning AMI (Ubuntu) Version 10.0 
- Instance type: p2.xlarge 
- Python Packages: Keras 2.1.6, Tensorflow, Opencv 

Examples of some image chips (the labels were added for readers). Each image chip consists of multiple labels and is separated by commas, such as clear, cloudy, hazy, road, water, cultivation, artisanal mine, and blooming. The dataset is quite challenging as the labels are highly imbalanced with a few labeling errors. Red, blue, and purple indicates <span style="color:SALMON"> atmospheric conditions</span>,  <span style="color:LightSkyBlue"> common land uses</span> and  <span style="color:Plum"> rare land uses</span>.

*Note: artisanal mine is another word for illegal mine*

<p float="left">
<img src="https://live.staticflickr.com/65535/49626992868_557450fa33.jpg" width="50%" />  <img src="https://lh3.googleusercontent.com/jn0yWdVFz-RplTsir-DZcRs0UYWSouwjwhknKi3J6-f-o4TPWBlL2AGNsKQa0NIBkPJ66XfUfKrB03-BmHo8vDq2dJhf6lZLRuhQmluBukP2V979NtW7NZ-5odX8mhEru029s6PDy40" width="49%" />
 </p>

#### Build a Neural Network Model

------

I started with a basic convolutional neural network (CNN) model as CNN is good at recognizing visual patterns in images. In order to train the CNN, images of forests, roads, water, agricultural fields, and other categories were provided to the model, along with images of illegal mining activities. The CNN then uses a pooling process to extract the most important information from the features it has found and reduce the amount of data it needs to store. Finally, it uses a process called fully connected layers to combine all of the information it has learned about the features to generate labels for the images. Although I had 70,000 clearly labeled training images, the results were not as good as expected. After 10 hours of training, the CNN model failed to predict any rare land use labels, indicating that it was underfit. The challenge then became how to improve the model to classify minority classes.

Two approaches I made to solve the problem:

<ul>
    <li>Improve input image quality</li>
    <li>More advanced network designs</li>
</ul>



##### **Improve image quality**

Generally speaking, clearer images that contain more information yield better results for neural networks. However, an image covered by haze can be challenging for the neural network to extract the necessary information. To address this issue, a dehaze function was developed based on a paper called: ["Single Image Haze Removal using Dark Channel Prior"](https://www.robots.ox.ac.uk/~vgg/rg/papers/hazeremoval.pdf).  Haze or blur in images is often caused by the scattering of light in the atmosphere before it reaches the camera. To simplify the process, the scattered light intensity is estimated as a constant, which is approximated to the maximum pixel intensity of the darkest RGB channel. By subtracting this haze constant, the image can be restored and clearer information can be extracted.

<img style="width:80%;display:block;" src="https://www.researchgate.net/profile/Seung_Won_Jung2/publication/291385074/figure/fig14/AS:320880610693124@1453515307125/Formation-of-a-hazy-image.png" />



 Figure 2. Formation of a hazy image. Camera = Direct attenuation+Airlight

Using the dehaze function results in the elimination of haze and an enhancement in image contrast. Here are some examples of images before and after dehazing. The dehazing function is highly effective for both clear and hazy images, as shown in the examples.

<img style="width:80%;display:inline-block;" src="https://lh5.googleusercontent.com/kLzNKsQnGef5RmNFomjswAF6Fx37KHPC4mEP4zfHRfQZAKSUzEL-nEVMziqYLKAMHZ07v8vWbgYhfntR3l7KGLRKXKhAIaFDLrh20OsqbK6L_U7wHFfsR6JPf5-WwMKq4ToEtGNwHrM"/>

Figure3. Before and after applying dehaze function on hazy, partly cloudy and clear images

This shows significant improvement on both precision and recall, especially the rare land use case. As both metrics are important and fewer false negatives are preferred, F2 score is used to evaluate the model. It is a combination of precision and recall and it puts more weight on recall.

<img style="width:80%;display:inline-block;" src="https://lh6.googleusercontent.com/P4qLeEVjt-Xh1vPbRrR12i6W43sfm03gZnA5x4NAoSkD4rkqx5cYPlmu9EplmXZDWM0TDudJzw-OOGQIOJ26T4VAFf2sD6isNkzWEyyZJOosXJpH5xXg581AVMpYm1B8j007y6BbdXk" />

Figure 4. Comparison of DenseNet model trained on original images and haze free images

##### **DenseNet**

Increasing model complexity is necessary to learn finer details. After research, I chose one of the latest Neural Network architectures: DenseNet (Dense Convolutional Network), a smarter neural network published by [Zhuang Liu and Gao Huang](https://arxiv.org/pdf/1608.06993v3.pdf) in 2017.  The major difference is that DenseNet connects each layer to every other layer whereas traditional convolutional network layers connect sequentially. DenseNet improves the flow of information and gradients throughout the network, therefore it has better parameter efficiency resulting in a faster training time. 

#### Work Flow

------

<img src="https://live.staticflickr.com/65535/49627827697_8058d80cab_c.jpg" width="80%" alt="work flow">

#### Model Performance

------

I trained the Dense Net model on the hazed removed image sets for 4 hours and saved the model as b01_dense121.h5. Then used it to generate labels for unlabelled test set images. Here are some examples of test photo chips marked as illegal mines. 

<img src="https://live.staticflickr.com/65535/49627526151_a93a067f6d_c.jpg" width="80%" alt="LSL0RMq">

The averaged precision and recall for all labels is 0.89 and 0.81. Out of 90,000 test images, 78 illegal mine photos were successfully detected, which gives precision of 0.69 and recall of 0.45. 

Project in posted in my [GitHub](https://github.com/mumuxi15/metis_proj/tree/master/Multilabel%20image%20classification). Thanks for reading !