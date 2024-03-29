# **Traffic Sign Recognition** 

---

**Build a Traffic Sign Recognition Project**

The goals / steps of this project are the following:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report


[//]: # (Image References)

[image1]: ./writeup_images/dist.png "Visualization"
[image2]: ./examples/grayscale.jpg "Grayscaling"
[image3]: ./examples/random_noise.jpg "Random Noise"
[image4]: ./new_images/60_kmh.jpg "Traffic Sign 1"
[image5]: ./new_images/left_turn.jpeg "Traffic Sign 2"
[image6]: ./new_images/road_work.jpg "Traffic Sign 3"
[image7]: ./new_images/stop_sign.jpg "Traffic Sign 4"
[image8]: ./new_images/yield_sign.jpg "Traffic Sign 5"

## Rubric Points
### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/481/view) individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one. You can submit your writeup as markdown or pdf. You can use this template as a guide for writing the report. The submission includes the project code.

You're reading it! and here is a link to my [project code](https://github.com/udacity/CarND-Traffic-Sign-Classifier-Project/blob/master/Traffic_Sign_Classifier.ipynb)

### Data Set Summary & Exploration

#### 1. Provide a basic summary of the data set. In the code, the analysis should be done using python, numpy and/or pandas methods rather than hardcoding results manually.

I used the pandas library to calculate summary statistics of the traffic
signs data set:

* The size of training set is 34799
* The size of the validation set is 4410
* The size of test set is 12630
* The shape of a traffic sign image is (32, 32, 3)
* The number of unique classes/labels in the data set is 43

#### 2. Include an exploratory visualization of the dataset.

Here is an exploratory visualization of the data set. It is a bar chart showing how the data is distributed

![alt text][image1]

### Design and Test a Model Architecture

#### 1. Describe how you preprocessed the image data. What techniques were chosen and why did you choose these techniques? Consider including images showing the output of each preprocessing technique. Pre-processing refers to techniques such as converting to grayscale, normalization, etc. (OPTIONAL: As described in the "Stand Out Suggestions" part of the rubric, if you generated additional data for training, describe why you decided to generate additional data, how you generated the data, and provide example images of the additional data. Then describe the characteristics of the augmented training set like number of images in the set, number of images for each class, etc.)

As a first step, I decided to convert the images to grayscale because color information doesn't help us identify important edges or other features in this problem and to reduce the complexity of the code. Our network has to detect signs by geometry not by color.

Here is an example of a traffic sign image before and after grayscaling.

![alt text][image2]

As a last step, I normalized the image data because its always better to have image data with zero mean and equal variance if its not case then it will be difficult for optimizer to find solution, well conditioned data i.e. zero mean and equal variance makes it lot easier to find optimum solution, image data comes in 0 to 255 pixle value, to normalize it I have used (X-Xmean)/std.

I decided to generate additional data using image augmentation because the model may overfit for a particular signs with more training data and cannot genralize for other.

To add more data to the the data set, I used the following techniques like rotate, zoom, shear, etc to expand the dataset.

No. of data after image augmantation = 215000

Here is an example of an original image and an augmented image:

![alt text][image3]

The difference between the original data set and the augmented data set is the following ... 


#### 2. Describe what your final model architecture looks like including model type, layers, layer sizes, connectivity, etc.) Consider including a diagram and/or table describing the final model.

My final model TrafficNet consisted of the following layers:

| Layer         		|     Description	        					| 
|:---------------------:|:---------------------------------------------:| 
| Input         		| 32x32x3 RGB image   							| 
| Convolution 5x5     	| 1x1 stride, same padding, outputs 28x28x6 	|
| RELU					|												|
| Max pooling	      	| 2x2 stride,  outputs 14x14x6 					|
| Convolution 5x5     	| 1x1 stride, same padding, outputs 10x10x16 	|
| RELU					|												|
| Max pooling	      	| 2x2 stride,  outputs 5x5x16 					|
| Convolution 5x5	    | 1x1 stride, same padding, outputs 1x1x400		|
| RELU					|												|
| Flatten layers		|8 (1x1x400 -> 400) and 6 (5x5x16 -> 400)		|
| Flatten layers concat | concat 400,400 to 800 						|
| Dropout               | keep_prob=0.5          						|
| Fully connected       | input=800, output=200 						|
| Fully connected       | input=200, output=100 						|
| Fully connected       | input=100, output=43 						    |
| Softmax				| output        								|
|						|												|



#### 3. Describe how you trained your model. The discussion can include the type of optimizer, the batch size, number of epochs and any hyperparameters such as learning rate.

To train the model, Initially I used same Lenet Modle as given in class tutorial, but validation accuracy of model was very low so To optimize it I have tried various approach to increase accuracy like adding droupout at fully connected layer, also changing keep probability value, additon of dropout at convolution 1 layer with different keep probability also ADAM optimizer gives better accuracy than SGD optimizer.

#### 4. Describe the approach taken for finding a solution and getting the validation set accuracy to be at least 0.93. Include in the discussion the results on the training, validation and test sets and where in the code these were calculated. Your approach may have been an iterative process, in which case, outline the steps you took to get to the final solution and why you chose those steps. Perhaps your solution involved an already well known implementation or architecture. In this case, discuss why you think the architecture is suitable for the current problem.

To acheive better validation accuracy I worked out on various hyperparameter like learning rate, epoch's, dropout keep probability and its position in network.

### Following parameter value gives best result for my network:
* Learning Rate = 0.0005
* Epoch = 30
* batch Size = 100
* Dropout = 0.5

My final model results were:
* training set accuracy of 0.982
* validation set accuracy of 0.958 
* test set accuracy of 0.921

If an iterative approach was chosen:
* What was the first architecture that was tried and why was it chosen?
    * I have used LeNet architecture as suggested in class tutorial.

* What were some problems with the initial architecture?
    * While working on LeNet architecture I have notice that accuracy of model not yielding enough due to image data was not pre-processed also the model overfitting data which result in poor validationn accuracy.
* How was the architecture adjusted and why was it adjusted? Typical adjustments could include choosing a different model architecture, adding or taking away layers (pooling, dropout, convolution, etc), using an activation function or changing the activation function. One common justification for adjusting an architecture would be due to overfitting or underfitting. A high accuracy on the training set but low accuracy on the validation set indicates over fitting; a low accuracy on both sets indicates under fitting.
    * Initial model was overfitting data and not yielding required accuracy for validation data set, so to avoid this we have introduced dropout with keep probability of 0.5.
* Which parameters were tuned? How were they adjusted and why?
    * Learning Rate, Batch Size, Epoch, Keep probability for dropout.
    * Learning Rate:- Higher Learning rate train model faster but stagnant earlier than acheving its full potential, whereas for lower learning rate model train slower but it achieves lowest possible loss for that model. in our model learning rate of 0.0005 yields better results.
    * Batch Size:- Since we can not train whole model at once due to computation power limitation, so we split model in batches, calculate all parameter for each batches and cascade it to top level for complete model, ats again on model size we can deside batch size, in our model Batch size is 128.
    * Epoch:- Epoch is number of iteration the data flows through the network, in our model it is 30. After is the learning is stagnant.
    * Keep Probability:- To avoid overfitting in model we have to introduced dropout at different layer, In our model I have added dropout before the first fully connected layer and the keep prob is set to 0.5.
* What are some of the important design choices and why were they chosen? For example, why might a convolution layer work well with this problem? How might a dropout layer help with creating a successful model?
    * Conv Layer 2 and Conv Layer 3 is flattened and concatenated to form a single vector as the input to fully connected layer.

If a well known architecture was chosen:
* What architecture was chosen?
    * Modified LeNet Architecture was used.
* Why did you believe it would be relevant to the traffic sign application?
    * LeNet model is sufficient for this application.
* How does the final model's accuracy on the training, validation and test set provide evidence that the model is working well?
    * The perfomance was between 95% to 98%
 

### Test a Model on New Images

#### 1. Choose five German traffic signs found on the web and provide them in the report. For each image, discuss what quality or qualities might be difficult to classify.

Here are five German traffic signs that I found on the web:

![alt text][image4] ![alt text][image5] ![alt text][image6] 
![alt text][image7] ![alt text][image8]


#### 2. Discuss the model's predictions on these new traffic signs and compare the results to predicting on the test set. At a minimum, discuss what the predictions were, the accuracy on these new predictions, and compare the accuracy to the accuracy on the test set (OPTIONAL: Discuss the results in more detail as described in the "Stand Out Suggestions" part of the rubric).

Here are the results of the prediction:

| Image			        |     Prediction	        					| 
|:---------------------:|:---------------------------------------------:|
| 60 km/h	      		| 80 km/h					 		            |
| Left-turn     		| Left-turn 									|
| Road work 			| Road work    							        |
| Stop Sign      		| Stop sign   									| 
| Yield					| Yield											|


The model was able to correctly guess 4 of the 5 traffic signs, which gives an accuracy of 80%.

#### 3. Describe how certain the model is when predicting on each of the five new images by looking at the softmax probabilities for each prediction. Provide the top 5 softmax probabilities for each image along with the sign type of each probability. (OPTIONAL: as described in the "Stand Out Suggestions" part of the rubric, visualizations can also be provided such as bar charts)

Image 0 probabilities: [ 10.19486046   8.63699341   6.43501663   5.76500225   0.87427998] 
and predicted classes: [ 5 10  3  9  7]

Image 1 probabilities: [ 40.74474335  14.83278656   4.07790041   1.40235186   1.0626272 ] 
 and predicted classes: [13 35 38 14  2]

Image 2 probabilities: [ 38.58807755  28.30526161  11.87184334   2.45600653   1.58123672] 
 and predicted classes: [34 38 30 35 12]

Image 3 probabilities: [ 14.16312218  10.40183163   0.74851191   0.24260345  -0.5646922 ] 
 and predicted classes: [14  3 25  1  0]

Image 4 probabilities: [ 9.13843155  8.94800472  8.42332458  7.52581978  3.19778538] 
 and predicted classes: [25  1 40 37 36]


