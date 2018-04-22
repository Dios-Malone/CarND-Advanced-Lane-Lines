## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---


### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the 1-3 code cells of the IPython notebook located in "./main.ipynb".

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objpoints` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![undistort_img](./output_images/undistort.PNG)

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![distort_img][./test_images/test3.jpg]

After applying undistortion, it looks like:
![distort_img](./output_images/undistorted_img.png)

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I tired multiple methods of thresholding including both color and gradient thresholds and comparing their results as following:
![comparison](./output_images/threshold_compare.PNG)

After comparison, I chose a combination of color and gradient thresholds to generate a binary image (thresholding steps at code cell 7-9 in `./main.ipynb`).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![threshold_img](./output_images/threshold_img.png)

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perspective_trans()`, which appears in code cell 4 in the file `./main.ipynb`.  The `perspective_trans()` function takes an image (`img`) as input.  I chose the hard-code the source and destination points in the following manner:

```python
src = [(600, 460), (250, 700), (1100, 700), (730, 460)]
dst = [(320,0), (320,720),(960,720),(960,0)]
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 600, 460      | 320, 0        | 
| 250, 700      | 320, 720      |
| 1100, 700     | 960, 720      |
| 730, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![point_img](./output_images/point_img.png)
![threshold_img](./output_images/transformed_img.png)

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I found out base x value of left and right lane pixels by searching the peaks of its histogram (code cell 11 in the IPython notebook) and used slide window to detect all pixels of the 2 lanes. Afterwards, function `polyfit()` from `numpy` was used to fit the lane lines with a 2nd order polynomial (code cell 12 - 17 in the IPython notebook) like this:

![poly_lines](./output_images/poly_lines.png)

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in code cell 23 - 27 in the IPython notebook. I just simply applied the formula provided in th course.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in code cell 20 through 22 in my code in `main.ipynb` in the function `warp_lane_back()` and `add_overlay()`.  Here is an example of my result on a test image:

![lane_overlay](./output_images/color_result.png)

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_result.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I think my pipeline may fail when there are very sharp turns, because the sliding window method seems assume the lanes are mostly vertical. And also I think the thresholding method may not work very well when there are complicated shadow patterns on the road (like in the forest).
