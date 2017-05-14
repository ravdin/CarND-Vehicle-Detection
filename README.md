
**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector.
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/test1.jpg
[image3]: ./output_images/test3.jpg
[video1]: ./project_out.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in lines 21 through 38 of the file called `lesson_functions.py`.  

This code is essentially copied from the lesson module.

#### 2. Explain how you settled on your final choice of HOG parameters.

I experimented with the parameters in the lesson plan for `HOG Classify`.  I found little need to deviate from the lesson.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

The classifier code is in the notebook file `detection.ipynb` in the section marked **Build a predictor and save the model**.  I used `sklearn.model_selection.GridSearchCV` to find the best parameters.  The classifier's accuracy is over 99%.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

The sliding window search is performed in `detection.ipynb` in the code section marked `Sliding windows search`.  I settled on the scales of `[1, 1.5, 2]` after some experimentation with the test images.  The windows overlap is carried over from the lesson code and I decided to keep it.

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image1]
![alt text][image3]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_out.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.

The code is in the notebook file `detection.ipynb` in the code section marked `Process the frame images`.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The biggest problem I had is that the processing time was very slow.  This was due to using multiple scales in the processing.  With one scale, the video processing took a fraction of the time and performed decently on the test video, but still missed a few cars.

I had a few ideas for improving the performance time and output that I didn't have time to fully explore:

* Use the box detection from previous frames to make the movement of the car smoother.
* Scale the detector based on the car's position.
* Use the x-axis in the detector.  If there is an obstacle directly in front of the car, we may need to take immediate action without classifying whether or not the obstacle is another car!
