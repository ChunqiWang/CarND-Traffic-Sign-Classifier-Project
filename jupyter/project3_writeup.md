# **Traffic Sign Recognition** 

**Build a Traffic Sign Recognition Project**

The goals / steps of this project are the following:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report


[//]: # (Image References)

[image1]: plots/TS_class_visualization.png "Train Data Visualization"
[image2]: plots/TS_class_distribution.png "Train Data Distirbution"
[image3]: plots/VS_class_distribution.png "Validation Data Distirbution"
[image4]: plots/preprocess.png "Image Pre-process Comparison"
[image5]: Plots/online_images_visualization.png "Online images"
[image6]: Plots/online_images_top_5_classes.png "Softwmax Top 5"

## Rubric Points
### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/481/view) individually and describe how I addressed each point in my implementation.  

---

### Data Set Summary & Exploration

#### 1. Data Summary

I used the pandas library to calculate summary statistics of the traffic
signs data set:

* The size of training set is 34799
* The size of the validation set is 4410
* The size of test set is 12630
* The shape of a traffic sign image is 32\*32\*3
* The number of unique classes/labels in the data set is 43

#### 2. Dataset Visualization.

The first figure in each class is shown below, the figure brightness vairies which enlighten the importance to pre-process the data

![Train Picture][image1]

Also the number of figures belong to each class in the train and validation data set is plotted. The figure distribution in the two data set is similar, the unique class that has more figures in the training set also has more in the validation set. But some classes has around 2000 figures while some only has 200, which can cause issues.

![Train Distribution][image2]

![TValidation Distribution][image3]

### Design and Test a Model Architecture

#### 1. Data Pre-processing

A basic three-step precpossing is applied on each image, grayscale, equalize and normalize.

As a first step, I decided to convert the images to grayscale because to accerlerate the training process, also in literture serach and my tests, adding the color channel doesn't improve the validation accuracy.

And then cv2 equalizeHist function is used to strech the histrogram and improve contrast. It can make the brighter and darker images more visible. 

As a last step, the image data is normalized with (pixel/128)-1, which brings each pixel data between -1 and 1 and makes the network to converge faster and reduces computational cost.

Below is one training image before and after the pre-processing

![Image Pre-process Comparison][image4]

#### 2. Model Architecture

My final model consisted of the following layers:

| Layer         		|     Description	        					| 
|:---------------------:|:---------------------------------------------:| 
| Input         		| 32x32x1 Grayscale image   					| 
| Convolution 5x5 (RELU)| 1x1 stride, valid padding, outputs 28x28x6 	|
| Max pooling	      	| 2x2 stride,  outputs 14x14x6				    |
| Convolution 5x5 (RELU)| 1x1 stride, valid padding, outputs 10x10x16   |
| Max pooling			| 2x2 stride,  outputs 5x5x16       			|
| Convolution 5x5 (RELU)| 1x1 stride, valid padding, outputs 1x1x120    |
| Flatten   			| 1x1x120-->120       				            |
| Dropout				| keep 75%, dropout 25% 						|
| Fully connected		| 120-->84										|
| Dropout				| keep 75%, dropout 25% 						|
| Fully connected		| 84-->43										|
 
#### 3. Model Training

Similar to the MINIST classift lab, below tensorflow function is used to train the model
    tf.nn.softmax_cross_entropy_with_logits, tf.reduce_mean and tf.train.AdamOptimizer

#### 4. Model Tuning 

My final model results were:
* training set accuracy of 99.0%
* validation set accuracy of 94.2%
* test set accuracy of 92.2%

Initially the same LeNet-5 model structure drom the MIIST classify lab is chosen. It's a baisc conveltional network and works in image processing. But while it can reach a realtively good accuracy in training set. The validation accuracy is significantly lower, which indicates overfitting. So two dropout layers are added to the network. And the first fully connected layer is changed to another convolution layer to bring more parameters to tune. With the current paramter setting it is a basically fully connected. It brings more flexibility as it will not be fully connected if the input image size is larger or the kernel size is smaller. Also here learning rate is increased to 0.002 to help overcome overfitting with dropout in 30 epoches.
 

### Test a Model on New Images

#### 1. Web Images

Here are ten German traffic signs that I found on the web, they are all converted to 32x32 pixel size

![Online Image][image5]

They are all taken in good weather so the images are brighter than the ones in training set. Also the traffic signs are larger and clearer. Initial thought is that they can be 100% classified.

#### 2. Model predictions on New Images 

Here are the results of the prediction:

| Image			        |     Prediction	        					| 
|:---------------------:|:---------------------------------------------:| 
| Speed limit(30km/h)   | Speed limit(30km/h)    						| 
| Children crossing    	| Children crossing								|
| Stop    	            | Stop							                |
| No vehicles			| No vehicles									|
| No entry	      		| No entry					 				    |
| Genreal caution		| Genreal caution     							|
| Turn right ahead		| Turn right ahead								|
| Priority road      	| Priority road 					 			|
| Yield		            | Yield               							|
| Speed limit(100km/h)  | Speed limit(80km/h)    						| 

The model was able to correctly guess 9 of the 10 traffic signs, which gives an accuracy of 90%. This actually compares favorably to the accuracy on the test set of 92.2%. 

#### 3. Softmax Probabilities on New images 

To further investigate into the prediction results, here the top 5 softmax pronilities on each image is calculated and shown on bar plots.

![Online Image Softmax][image6]

Among the correct predicted images, most of them get over 99% on the top 1 probability, indicates pretty good and accurate prediction. Worst probablity is 91.9% on Pirority road. 

On the wrong predicted image, Speed limit(100km/h). The top 5 softmax probabilities are:

| Probability         	|     Prediction	        					| 
|:---------------------:|:---------------------------------------------:| 
| .504        			| Speed limit(80km/h)   						| 
| .412    				| Speed limit(100km/h) 							|
| .076					| No passing for vehicle over 3.5 metric tons	|
| .007	      			| Roundabout mandatory					 		|
| .000				    | Priority road     							|

Good news is that the model predicts 41.2% on the right class. Here when looking at the Piorrity road and Speed limit(100km/h) images, the conversion to 32x32 images making the images more pixelated, also the perspective distotion is a bit strange on the 100 characters, which could be the reasons lead to wrong predictions. 
