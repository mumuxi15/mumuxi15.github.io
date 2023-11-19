---
layout: post
title:  "Amazon Illegal Mining Detection"
author: penny
categories: [ classification, supervised ]
image: assets/images/1-mining.jpeg
tags: [sticky]
---

The Amazon rainforest, known for its rich biodiversity and vital role in regulating the Earth's climate, has been facing a grave threat in the form of deforestation. This destructive practice has led to significant habitat destruction, biodiversity loss, and contributes to climate change. A key factor exacerbating this devastation is the sharp increase in illegal gold mining activities observed in the region over the past decade. Motivated by the soaring global gold price, individuals have engaged in unlawful mining practices within the Amazon rainforest, further exacerbating the environmental crisis.



Traditional labor-intensive methods involving the hiring of local citizens to spot illegal activities have proven to be costly and time-consuming. Fortunately, advancements in satellite technology have opened new avenues for detecting illegal mining activities using satellite images and employing machine learning techniques like image classification.



The primary objective of this project is to develop a system capable of identifying illegal mining activities among 16 other labels, including clouds, trees, ground, road, water, and more. To achieve this, I employed a dataset obtained from ["Planet: Understanding the Amazon from Space."](https://www.kaggle.com/c/planet-understanding-the-amazon-from-space) The dataset consisted of satellite images that were segmented into smaller chips and randomly assigned numerical names. Our aim was to generate accurate labels that effectively describe the content of each image and enable the identification of those chips associated with illegal mining. Neural networks were chosen as the preferred approach for this project due to their exceptional ability to recognize complex patterns and extract meaningful features from images. 



##### Start with basic Neural Network Model

<p float="left">
    <img src="https://live.staticflickr.com/65535/49626992868_557450fa33.jpg" width="50%" />  <img src="https://lh3.googleusercontent.com/jn0yWdVFz-RplTsir-DZcRs0UYWSouwjwhknKi3J6-f-o4TPWBlL2AGNsKQa0NIBkPJ66XfUfKrB03-BmHo8vDq2dJhf6lZLRuhQmluBukP2V979NtW7NZ-5odX8mhEru029s6PDy40" width="49%" /> <em>Left Figure 1: Examples of image chips with labels.   Right Figure 2: Frequency distribution of labels</em></p>


The dataset consists of 70,000 image chips, each was associated with at least one label. The labels were categorized into three main groups: <span style="color:SALMON">  atmospheric conditions</span>,  <span style="color:LightSkyBlue"> common land uses</span> and  <span style="color:Plum"> rare land uses</span>.  However, there were significant imbalances in the distribution of the labels, and a few labeling errors were present in the dataset. Among the labels, less than 1% represented categories such as artisanal mines, conventional mines, slash burn, and others. This highly imbalanced nature of the labels posed a challenge during the training process. The neural network had to learn to accurately classify and recognize the rare land use categories, despite having limited examples for these classes.



##### Challenges of predicting rare used labels

My initial attempt involved using a random forest with computed features such as haziness, image contrast, and RGB color channels. However, this approach proved unsuccessful, as the model struggled to differentiate the most basic weather conditions. Consequently, attention shifted towards a convolutional neural network (CNN), known for its prowess in capturing and extracting essential local features for pattern and object identification. While successful in discerning weather labels and common land uses, the CNN faced challenges in predicting rare land use labels, despite extensive training. Even after implementing oversampling techniques, the model not only failed to improve accuracy but also exacerbated the recall score, resulting in misclassifications of other labels as mining activities.



In light of those challenges, I explored alternative approaches to improve model performance, particularly in predicting the 1% population representing rare land use categories. 



- Update computational power: use AWS GPU
- More advanced network designs
- Improve input image quality
- Rotate and flip images to increase the sample size



##### Update computational power

To harness the power of advanced hardware and accelerate the training process, an AWS GPU instance was utilized. The use of GPU computing combined with multi-processing technique allows for parallel processing and faster model training, enabling more efficient experimentation and optimization.   Additionally, cross-validation was implemented to continuously monitor the model's performance across multiple iterations, ensuring that overfitting issues were identified and addressed promptly.



##### Advanced network designs - DenseNet

To further improve the model's performance and accuracy, a customized neural net model was implemented using DenseNet architecture. This deep neural network design introduces dense connections between layers, allowing for direct information flow and gradient propagation throughout the network. This architecture alleviates the vanishing gradient problem, encourages feature reuse and leads to better parameter efficiency and faster convergence during training.The Python package named Keras was employed to create and connect different neural network layers, with binary cross-entropy serving as the loss function. 

$$\text{Binary Crossentropy } = -\sum y_i log({\hat{y}}_i)+(1-y_i)log({1-\hat{y}}_i) $$

, where y is the true probability and ŷ  is the predicted probability. The binary cross-entropy loss function is particularly effective in multi-class problems. In this context, let's consider a three-class problem with $y_A=1, y_B=1, y_C=0$.  Suppose the model predicts $ŷ_A=0.4, ŷ_B=0.7, ŷ_C=0.1$  The loss is calculated as -(log(0.4)+log(0.7)+log(0.9)) = 0.60. As the predicted y of A and B approaches the correct answer 1, the loss function becomes small. Conversely, if predicted probability of A is close to zero, the loss becomes a large number. Same applies to $y_C$ when it approaches to 0. 



As a result, the new model benefits from requiring fewer parameters and faster convergence compared to CNN. It accelerates the training process and mitigates overfitting risk. This approach encourages the model to learn more robust and generalized features from the data, ultimately improving performance and accuracy.



##### Improve image quality

Satellite images often suffer from darkness and blurriness caused by atmospheric turbulence. However, by mitigating the effects of haze and improving image quality, we can enhance the performance of neural networks. Leveraging my understanding of atmospheric physics and conducting research on Google Scholars, I developed a dehaze function based on a [paper](https://www.robots.ox.ac.uk/~vgg/rg/papers/hazeremoval.pdf) to address this challenge. Haze results from the scattering of light in the atmosphere before it reaches the camera. To estimate the intensity of scattered light, a constant value is derived by approximating the maximum pixel intensity within the darkest RGB channel. By utilizing OpenCV to convert images to a colorspace matrix and calculating the haze constant, we can restore the image by subtracting this value. As a result, the dehazed image appears brighter and exhibits better contrast, improving the quality of input data for neural networks.

<img src="https://www.researchgate.net/profile/Seung_Won_Jung2/publication/291385074/figure/fig14/AS:320880610693124@1453515307125/Formation-of-a-hazy-image.png" width="80%" />
<em>Figure 3: Formation of a hazy image. Camera = Direct attenuation+Airlight</em>

<img src="https://live.staticflickr.com/65535/53340498376_0a75c43194_c.jpg" width="80%" />
<em>Figure4 : Examples of satellite image chips before and after haze removal</em>

The impact of the dehazing process is evident, as it leads to a substantial improvement in image quality across most samples. Upon the application of the haze removal function, the image undergoes a remarkable transformation. The contrast is restored, bringing out more vivid and distinct features. The sharpness of objects is improved, and details that were previously obscured by haze become more visible. Colors, regaining vibrancy and accuracy, contribute to a clearer and more faithful representation of the scene. Overall, the haze removal function significantly elevates the image visibility and quality by reducing the impact of atmospheric interference.



The choice of F2 score as the performance metric is deliberate due to the uneven distribution of label classes, where recall takes precedence over precision. Recall measures the ability to capture all positive instances correctly. In the specific context of identifying labels associated with mining, a high recall implies the model's effectiveness in detecting actual mining instances, thereby minimizing the occurrence of false negatives (missed mining instances). The F2 score, by assigning greater weight to recall, ensures that the model adeptly identifies as many positive instances as possible while maintaining a reasonable level of precision. This strategic emphasis aligns with the objective of optimizing performance in scenarios where missing positive instances carries a higher cost than false positives.

<img style="width:80%;display:inline-block;" src="https://lh6.googleusercontent.com/P4qLeEVjt-Xh1vPbRrR12i6W43sfm03gZnA5x4NAoSkD4rkqx5cYPlmu9EplmXZDWM0TDudJzw-OOGQIOJ26T4VAFf2sD6isNkzWEyyZJOosXJpH5xXg581AVMpYm1B8j007y6BbdXk" />

<em>Figure 5: F2 score before (purple) and after (blue) the haze removal function. Notice it dramatically improve the results for rare land use labels</em>

##### Work Flow

<img src="https://live.staticflickr.com/65535/49627827697_8058d80cab_c.jpg" width="80%" alt="work flow">

##### Model performance

After training the DenseNet model on the dehazed image sets, the model was saved as "b01_dense121.h5". This trained model was then utilized to generate labels for the previously unlabelled test set images. The model successfully identified several test photo chips as illegal mines, providing valuable insights into the presence of illegal mining activities in the dataset.

<img src="https://live.staticflickr.com/65535/49627526151_a93a067f6d_c.jpg" width="100%"><em>Figure 6. Examples from the test data that were marked as illegal mines with labels generated by the DenseNet model.</em>



In summary, the project focused on developing a model to detect illegal mining and rare land use cases using satellite images. Several approaches were employed to address the challenges of imbalanced labels, low image quality, and underfitting. These approaches included improving computational power, rotating images of rare land use categories, using more advanced network designs like DenseNet, and enhancing input image quality through haze removal. The implementation of these approaches resulted in significant improvements in both precision and recall, particularly for the rare land use cases. 











