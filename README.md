# Vehicle Detection
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

[HOG]: ./output_images/hog-visualization.jpg "HOG"
[dataset]: ./output_images/image_example.png "Dataset Example"
[windows]: ./output_images/search_windows.png "Search Windows"
[windows_vehicle]: ./output_images/windows_with_vehicle.png "Windows with Vehicle"
[car_detected]: ./output_images/car_detected.png "Windows with Vehicle"

The Project
---

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Implement a sliding-window technique and use your a classifier to search for vehicles in images.
* Run the pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.


### Histogram of Oriented Gradients (HOG)

The histogram of oriented gradients (HOG) is a feature descriptor used in computer vision and image processing for the purpose of object detection. The technique counts occurrences of gradient orientation in localized portions of an image. In this project, I use HOG method to detect vehicles.

HOG calculate the gradient of a cell (a small part of image) in several given directions. I explored several different parameters that seems worthy of trying, e.g. different channels, spatial bin size, pixels per cell and cells per block, and their combinations. 

Finally, based on the speed and accuracy, I choosed the `hog` function with `32x32` gray image, `8` pixels per cell, `2` cells per block, `8` orients. 


These gradient value can be used as feature for a classifier.

![][HOG]

### Vehicle classifier

The training data contains 11,631 vehicle images and 8,971 non-vehicle images. All the image is 64x64 pixels.

![][dataset]

I use a Linear Support Vector Machine classifier to classify the vehicles.

(LinearSVC in sklearn.svm package)

Finally, I use only the HOG of Grey image as the features to train the SVM classifier. The feature's length is 324.

(I tried HOG for the saturation channel, history information of pixels. In conisderate both the speed and accuracy, I use HOG of Grey only.)

The data set is split to trainning data and testing data in a ratio 4:1

Classifier performance:

Test Accuracy of SVC =  0.9364

I also tried Classification Tree, Convolutional Neural Network. Classification Tree's accuracy is not enough. CNN is a little bit slow but have no better result than SVM model.


### Sliding Window Search

I defined a set of windows for searching windows. Different window sizes are used for car far, middle and near. In total 416 of detection windows are used.

![][windows]

For each window, the picture is extracted and I detected if there is a vehicle in this window.

![][windows_vehicle]

These windows are overlap with each other. Thus a vehicle will be detected several times. We use these positive detection generate a heat map. Only value > 2 in the heat map will be hold. Based on the heat map, we draw a rectangle to indicate the position of the vehicle.

![][car_detected]


### Video Implementation

To detect the vehicles in a video, I use a queue to keep the record of the heat map of last 5 images.
By applying another threshold, I remove maximun false positive. 


### Discussion

1. When I use histogram feature of the image, the accuracy on the validation image increased. However, the error seems increased on real detection. I think there might a bais in our training set.
2. The accuracy and the speed are both important. We need to detect 416 images for each frame, to find vehicles.
3. It is possible to reused the result of the last frame. For example, if a car is detected, we can track it with template matching. This can accelerate the speed and also allow us to track the movement.