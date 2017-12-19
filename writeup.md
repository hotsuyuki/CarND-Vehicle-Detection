# Vehicle Detection Project

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector.
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image2]: ./examples/HOG_example.jpg
[image3]: ./examples/sliding_windows.jpg
[image4]: ./examples/sliding_window.jpg
[image5]: ./examples/bboxes_and_heat.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in a function called `extract_features()` at the 2nd code cell (In [2]:) of `P5.ipynb`.  

I started by reading in all the `cars` and `notcars` images.  Here is an example of one of each of the `cars` and `notcars` classes at the 3rd code cell (In [3]:) of `P5.ipynb`:

<div style="text-align:center"><br/>
<img src="./output_images/car_not_car.png"><br/>
A car and a Not-car<br/><br/>
</div>

I then explored each color channel, Y, U, V respectively. I grabbed random images from the car classe and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YUV` color space and HOG parameters of `orientations=12`, `pixels_per_cell=(16, 16)` and `cells_per_block=(2, 2)`:

<div style="text-align:center"><br/>
<img src="./output_images/hog_yuv.png"><br/>
HOG features with YUV color channel<br/><br/>
</div>

#### 2. Explain how you settled on your final choice of HOG parameters.

I decided the color space and these parameters reffering to [this GitHub repository](https://github.com/jeremy-shannon/CarND-Vehicle-Detection). However I changed the `orientations` from `11` to `12` in order to make it straightforward to devide from 360 degree.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features and color features if you used them.

Before training, I added two color features, Spatial Binning and Color Histograms, to HOG features.

<div style="text-align:center"><br/>
<img src="./output_images/spatial_binning.png"><br/>
Spatial Binning<br/>
</div>

<div style="text-align:center"><br/>
<img src="./output_images/color_histograms.png"><br/>
Color Histograms<br/><br/>
</div>

Then I normalized the concatenated features and trained a linear SVM with `C=1` (default) using color and HOG features at the 4th code cell (In [4]:) of `P5.ipynb`.

<div style="text-align:center"><br/>
<img src="./output_images/normalized.png"><br/>
Normalized Features<br/><br/>
</div>

The test accuray of the SVC was `0.9947`, which is pretty high.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to search random window positions at random scales all over the image and came up with this (ok just kidding I didn't actually ;):

![alt text][image3]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image4]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps:

![alt text][image5]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:
![alt text][image6]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image7]



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
