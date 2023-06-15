---
layout: post
title:  "Amazon Illegal Mining Detection"
author: penny
categories: [ classification, supervised ]
image: assets/images/1-mining.jpeg
tags: [sticky]
---

The Amazon rainforest, known for its rich biodiversity and vital role in regulating the Earth's climate, has been facing a grave threat in the form of deforestation. This destructive practice has led to significant habitat destruction, loss of biodiversity, and contributes to climate change. A key factor exacerbating this devastation is the sharp increase in illegal gold mining activities observed in the region over the past decade. Motivated by the soaring global gold price, individuals have engaged in unlawful mining practices within the Amazon rainforest, further exacerbating the environmental crisis.



Traditional labor-intensive methods involving the hiring of local citizens have proven to be costly and time-consuming. Fortunately, advancements in satellite technology have opened new avenues for detecting illegal mining activities using satellite images and employing machine learning techniques like image classification.



The primary objective of this project is to develop a system capable of identifying illegal mining activities among 16 other labels, including clouds, trees, ground, road, water, and more. To achieve this, I employed a dataset obtained from ["Planet: Understanding the Amazon from Space."](https://www.kaggle.com/c/planet-understanding-the-amazon-from-space) The dataset consisted of satellite images that were segmented into smaller chips and randomly assigned numerical names. Our aim was to generate accurate labels that effectively describe the content of each image and enable the identification of those chips associated with illegal mining. Neural networks were chosen as the preferred approach for this project due to their exceptional ability to recognize complex patterns and extract meaningful features from images. 



#### Start with basic Neural Network Model



<p float="left">
<img src="https://live.staticflickr.com/65535/49626992868_557450fa33.jpg" width="50%" />  <img src="https://lh3.googleusercontent.com/jn0yWdVFz-RplTsir-DZcRs0UYWSouwjwhknKi3J6-f-o4TPWBlL2AGNsKQa0NIBkPJ66XfUfKrB03-BmHo8vDq2dJhf6lZLRuhQmluBukP2V979NtW7NZ-5odX8mhEru029s6PDy40" width="49%" />
 </p>

The dataset consists of 70,000 image chips, each was associated with at least one label. The labels were categorized into three main groups: <span style="color:SALMON">  atmospheric conditions</span>,  <span style="color:LightSkyBlue"> common land uses</span> and  <span style="color:Plum"> rare land uses</span>.  However, there were significant imbalances in the distribution of the labels, and a few labeling errors were present in the dataset. Among the labels, less than 1% represented categories such as artisanal mines, conventional mines, slash burn, and others. This highly imbalanced nature of the labels posed a challenge during the training process. The neural network had to learn to accurately classify and recognize the rare land use categories, despite having limited examples for these classes.



I began by implementing a basic convolutional neural network (CNN) model. One of the key aspects is its ability to capture and extract important local features, allowing them to identify patterns and objects. However, after hours of training, the model struggled to predict any rare land use labels. The challenge here is how to predict the 1% population. 



Approaches I made to solve the problem:

- Update computational power: use AWS GPU
- More advanced network designs
- Improve input image quality



##### Update computational power

To harness the power of advanced hardware and accelerate the training process, an AWS GPU instance was utilized. The use of GPU computing combined with multi-processing technique allows for parallel processing and faster model training, enabling more efficient experimentation and optimization.   Additionally, cross-validation was implemented to continuously monitor the model's performance across multiple iterations, ensuring that overfitting issues were identified and addressed promptly.



##### Advanced network designs - DenseNet

To further improve the model's performance and parameter efficiency, a DenseNet architecture was implemented. DenseNet is a deep neural network architecture that introduces dense connections between layers, allowing for direct information flow and gradient propagation throughout the network. This architecture alleviates the vanishing gradient problem and encourages feature reuse, leading to better parameter efficiency and faster convergence during training. As a result, the model benefits from requiring fewer parameters to achieve similar or even superior performance compared to CNN. This not only accelerates the training time but also helps mitigate the risk of overfitting by encouraging the model to learn more robust and generalized features from the data.



##### Improve image quality

Satellite images often suffer from darkness and blurriness caused by atmospheric turbulence. However, by mitigating the effects of haze and improving image quality, we can enhance the performance of neural networks. Leveraging my understanding of atmospheric physics and conducting research on Google Scholars, I developed a dehaze function based on a [paper](https://www.robots.ox.ac.uk/~vgg/rg/papers/hazeremoval.pdf) to address this challenge. Haze results from the scattering of light in the atmosphere before it reaches the camera. To estimate the intensity of scattered light, a constant value is derived by approximating the maximum pixel intensity within the darkest RGB channel. By utilizing OpenCV to convert images to a colorspace matrix and calculating the haze constant, we can restore the image by subtracting this value. As a result, the dehazed image appears brighter and exhibits better contrast, improving the quality of input data for neural networks.

<img style="width:80%;display:block;" src="https://www.researchgate.net/profile/Seung_Won_Jung2/publication/291385074/figure/fig14/AS:320880610693124@1453515307125/Formation-of-a-hazy-image.png" />



Figure 2. Formation of a hazy image. Camera = Direct attenuation+Airlight

<p float="left">
<img src="https://live.staticflickr.com/65535/49626992868_557450fa33.jpg" width="50%" />  <img src="https://lh3.googleusercontent.com/jn0yWdVFz-RplTsir-DZcRs0UYWSouwjwhknKi3J6-f-o4TPWBlL2AGNsKQa0NIBkPJ66XfUfKrB03-BmHo8vDq2dJhf6lZLRuhQmluBukP2V979NtW7NZ-5odX8mhEru029s6PDy40" width="49%" />
 </p>

Figure 3 showcases several examples of images before and after applying the haze removal function. The impact of the dehazing process is evident, as it leads to a substantial improvement in image quality across most samples. The "before" images appear hazy and lackluster, with reduced visibility and contrast. However, upon applying the haze removal function, the "after" images exhibit a remarkable transformation. The images become brighter, with enhanced contrast and sharper details. This dehazing technique proves particularly effective for images tagged as partly cloudy or affected by haze, successfully restoring their visual appeal and making them more suitable for analysis and interpretation.

The results of the model demonstrate a notable enhancement in both precision and recall, particularly in the case of rare land use labels. Given the significance of these metrics and the preference for minimizing false negatives, the evaluation of the model's performance is based on the F2 score. The F2 score is a composite metric that combines precision and recall, with a greater emphasis on recall. In scenarios where the consequences of missing positive instances are significant, such as detecting illegal activities or identifying rare occurrences, a higher recall is crucial. By assigning more weight to recall, the F2 score ensures that the model effectively identifies as many positive instances as possible, while still maintaining a reasonable level of precision. 

<img style="width:80%;display:inline-block;" src="https://lh6.googleusercontent.com/P4qLeEVjt-Xh1vPbRrR12i6W43sfm03gZnA5x4NAoSkD4rkqx5cYPlmu9EplmXZDWM0TDudJzw-OOGQIOJ26T4VAFf2sD6isNkzWEyyZJOosXJpH5xXg581AVMpYm1B8j007y6BbdXk" />

##### Work Flow

<img src="https://live.staticflickr.com/65535/49627827697_8058d80cab_c.jpg" width="80%" alt="work flow">

##### Model performance

After training the DenseNet model on the dehazed image sets, the model was saved as "b01_dense121.h5". This trained model was then utilized to generate labels for the previously unlabelled test set images. The model successfully identified several test photo chips as illegal mines, providing valuable insights into the presence of illegal mining activities in the dataset.

<img src="https://live.staticflickr.com/65535/49627526151_a93a067f6d_c.jpg" width="80%" alt="LSL0RMq">

Figure 4. Examples from the test data that were marked as illegal mines with labels generated by the DenseNet model.



In summary, the project focused on developing a model to detect illegal mining and rare land use cases using satellite images. Several approaches were employed to address the challenges of imbalanced labels, low image quality, and underfitting. These approaches included improving computational power, rotating images of rare land use categories, using more advanced network designs like DenseNet, and enhancing input image quality through haze removal. The implementation of these approaches resulted in significant improvements in both precision and recall, particularly for the rare land use cases. 











