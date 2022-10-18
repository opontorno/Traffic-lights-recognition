# **Traffic lights recognition: notebook explanation**

## **Introduction**
---
The basic idea of this report is to build a model emulating the behaviour of an autonomous driving car in the management of the action to be taken upon recognition of a traffic light: if the light is off, ignore it, and if the light is on, stop, slow down or continue, depending on the colour. The algorithm must therefore first be able to recognise traffic lights and then classify them according to four classes: off, green, yellow and red. It is therefore a matter of training a network in performing object detection and classification.

To do this, the pre-trained network '*YOLO*' in its fifth version will be used. YOLO an acronym for 'You only look once', is an object detection algorithm that divides images into a grid system. Each cell in the grid is responsible for detecting objects within itself. YOLO is one of the most famous object detection algorithms due to its speed and accuracy (*https://github.com/ultralytics/yolov5*). Once this network is taken, a fine-tuning of the hyper-parameters will be done, so as to force the network to concentrate only on recognising the traffic lights by giving it as input a series of images with relative labels, which specify the coordinates of the bounded boxes of each traffic light. The network's objective will be to learn to find the coordinates of any bounded boxes of traffic lights in an image. In addition, YOLO not only recognises the objects in the images, but is also able to classify them. In fact, by specifying in each traffic light label the class to which it belongs, the network will also be able to classify by itself the class of each new traffic light.

The notebook is structured in three parts: a first preliminary phase, the 'Data Visualization phase', the 'Model-building phase' and the 'Testing phase'.

In the first part, the libraries used for the data visualisation phase and the model building phase are implemented. Below is shown the procedure for downloading the dataset from *kaggle.com* and how to make it usable in your own work directory.

## **Data Visualization phase**

The second part of the notebook consists of visualising the images used to train the model. Initially, five images randomly taken from the train dataset are displayed. 

![datavis](https://user-images.githubusercontent.com/107320990/196508730-df69e962-59f9-43ff-87c1-bbf61b5c337d.png)

It is already possible to realise how all images contain one or more traffic lights in a more or less obvious manner; these can be on or off.

As already mentioned, the file with the *.json* extension contains information about each traffic light in the training set. All that matters to us are the coordinates of the bounded boxes for each traffic light and the color in the event that it is on in the image.

At this point, we want to check the correctness of these bounded boxes. To do this, we initially wrote a function that returns a string containing a five-digit number between '00000' and '03000'. The utility of this function is to search for images in the train dataset simply by attaching the string containing the identification number of each image to a string containing the common path of each image. In addition, the same generated string is used to search within the json file for the coordinates of all bounded boxes within the image.

The result of this operation is to show an image of the training dataset with all the bounded boxes that identify the traffic lights within the image drawn on it.

![examplebb](https://user-images.githubusercontent.com/107320990/196508909-5f70aadd-64a8-4bb0-a8e7-0f2a6ecb42e1.png)


## **Model-building phase**

The third stage of the notebook is where the model capable of recognising the traffic lights in the images is actually built. As already mentioned in the introduction, this model is based on the pre-trained yolov5 network.

Once yolov5 has been downloaded, a preliminary preparation phase is necessary. We need to correctly annotate the training images so that we can use them to train the yolo network. To do this, we used the online tool 'Roboflow' (available at *https://roboflow.com/*). Both the folder containing all the train images and the json file containing all the annotations were passed to the online tool Roboflow. From this, two folders were obtained: one containing the images and the other containing the text files (labels) with the co-ordinates of all the traffic lights written in them. The traffic lights are classified into four different classes: 'red', 'yellow', 'green' and 'object'. The first three are the traffic lights that are on in the images, those that are not to be ignored (i.e. those that have the entry 'ignore : 0' in the json file), while the traffic lights of the last class are those that are off in the images.

The next step was to divide the training dataset into two parts: a first part, containing 80 per cent of the images, which was used to train the model, and a second part to serve as a validation set. The role of the validation set is to measure the performance of the model. In fact, the algorithm, after being trained using the training set, will test its ability to recognise traffic lights on new images that it has never seen and that it has not used for training. The predicted results will then be compared with the expected results, thus calculating metrics that will measure the model's skill.

At this point we can move on to the training phase of the model. We decided to train the model using yolov5's 's' weights, which performed well, with batch equals 16 and a number of epochs equal to 30.

Below are the metrics obtained from the model as the epochs increase.

![results](https://user-images.githubusercontent.com/107320990/196508993-bbd5ca6b-1367-4191-955d-31751e4175f0.png)
![metrics](https://user-images.githubusercontent.com/107320990/196509051-da632571-cd27-4735-8072-c02593689328.png)


We note that almost all learning curves follow an optimal trend, which leads us to assume that the model has correctly learnt to detect and classify traffic lights.

Some of the images of the validation set on which the model was validated are shown below. In particular, on the left, the images contain the bounded boxes generated by the model, and on the right, the actual bounded boxes.

![valimgs](https://user-images.githubusercontent.com/107320990/196509177-4df8a651-cfc5-4849-8f92-cddbec6b75e1.png)


We can see that, in general, the model manages with good accuracy both to recognise traffic lights and to classify their status (off, on with red light, on with yellow light or on with green light).

## **Results**
Once the model has been built and validated, it is time to test it in the field. In the testing phase, the model is asked to recognise and classify the traffic lights of the testing images, which, unlike the training and validation images, contain no labels. Therefore, it is up to human supervision to tell whether the model makes mistakes in recognising and classifying objects. We now show the result of applying the algorithm to a test image.

![detect](https://user-images.githubusercontent.com/107320990/196509257-8456d024-846d-4e2d-b569-feed6961c18b.jpeg)


From this image comes further confirmation that the model can correctly classify traffic lights. 
