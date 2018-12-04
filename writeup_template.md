## Writeup 

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

[image1]: ./output_images/calibration3_undistort.jpg "Undistorted"
[image2]: ./test_images/test2.jpg "Road Transformed"
[image3]: ./output_images/thresholded_binary.jpg "Binary Example"
[image4]: ./output_images/unwarped_transformed_binary.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./output_images/camera_view_with_lanes.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "./project2.ipynb"
I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines 6 through 31 in `undistort_threshold_image()`).  Here's an example of my output for this step.
![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in lines 32 through 47 in function `undistort_threshold_image()` in the file `./project2.ipynb`. I chose the hardcode the source and destination points in the following manner:
find a region that include lanes and project that region to a persepctive view of a complete image size 
This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 0, 720      | 0, 720        | 
| 565, 450      | 0, 0      |
| 695, 450     | 1280, 0      |
| 1280, 720     | 1280, 720      |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `measure_curvature_pixels()` in `./project2.ipynb`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines 2 through 7 in my code in function `process_image()` .  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./test_videos_output/project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?


##### 1. Consideration of problems/issues faced.
What were the difficulties encountered during project implementation? How were these issues/difficulties overcomed?
The shadows in the video introduce a lot of noises that caused  my polynomial fit to fail. It could not fit two parallel curves. I fixed this by adding a filter of lightness since shadow has low lightness value. This works pretty well and filter out almost all the noise.

##### 2. what could be improved.
Is there any particular part of the model that could be improved?Are there other better ways of lane detection?
The perspective tranform is manually defined in terms of src and dst points. This is not reliable since this may fit well for some videos but not for other videos, depending on the camaero position and road condition
If the line of sight is short, for example if there is big curves in front of the vehicle, the line detection may not work well because I defined the region mask manually 
A better solution should be defineing perspective transform using some automatic algorithm.

##### 3. what hypothetical cases would cause the pipeline to fail.
The pipelin is assuming there is no obstacles in front of the camera that may introduce noise for lane detection.
If there is any object in front of the camera, like a vehicle nearby or a biker, this pipeline may fail.
