#Advanced Lane Finding Project

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

[image1]: ./output_images/CameraCalibration/CameraCalibration.png "Undistorted"
[image2]: ./output_images/Undistort/Undistort1.png "Undistorted Test Images"
[image3]: ./output_images/HLS_Sobel_Thresholded/HLS_Sobel_Thresholded1.png "Binary Example"
[image4]: ./output_images/PerspectiveTransform/PerspectiveTransform1.png "Warp Example"
[image5]: ./output_images/LaneDetection_CurveFitting/LaneDetection_CurveFitting1.png "Fit Visual"
[image6]: ./output_images/FinalProcessedImages/ProcessedImage1.png "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

###Camera Calibration

The code for this step is contained in the code cell 2 and 3 of the IPython notebook located in "./AdvancedLaneFinding/AdvLaneFinding.ipynb" 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
You can find other undistorted images at [UNDISTORTED IMAGES](https://github.com/Jasmamu1992/AdvancedLaneFinding/tree/master/output_images/Undistort)

![alt text][image2]

####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at code cells 5,6,7 in `./AdvancedLaneFinding/AdvLaneFinding.ipynb`).  Here's an example of my output for this step.

![alt text][image3]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perspective_transform()`, which appears in code cell 4 in the file `./AdvancedLaneFinding/AdvLaneFinding.ipynb`.  The `perspective_transform()` function takes as inputs an image (`img`), as well as Perspective_Transform Matrix (`M`) which is obtained using `cv2.getPerspectiveTransform(src, dst)`.  I chose the hardcode the source and destination points in the following manner:

```
src = np.float32([[557,460],[732,460],[100,720],[1200,720]])
dst = np.float32([[0,0],[1200,0],[0,720],[1200,720]])

```
This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 557, 460      | 0, 0          | 
| 732, 460      | 1200, 0       |
| 100, 720      | 0, 720        |
| 1200, 720     | 1200, 720     |

I tested the Perspective transform on the test images as follows

![alt text][image4]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I divided the warped image into 6 parts and took histogram for each of theses parts. Then I found the peaks in the histogram and started searching for any line points situated araound the peaks. Then I differentiated the points to leftlanepoints and rightlane points based on their position in the image(all the implemetation is done in a function called `Hist()` which can be found in code cell 8 of `./AdvancedLaneFinding/AdvLaneFinding.ipynb`) and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in code cell 8 in `./AdvancedLaneFinding/AdvLaneFinding.ipynb`

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step code cell 10 in `./AdvancedLaneFinding/AdvLaneFinding.ipynb` in the function `Process()`.  Here is an example of my result on a test image:

![alt text][image6]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_out.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I felt the most difficult part of this project is in getting the sobel and HLS thresholding parameters right and to implement the histogram based method for detecting left and right lane points. 

When I tested the pipeline I implemented on challenge_video, I found that the pipeline is mostly failing in sobel and HLS thresholding part, because it is picking up lot of unwanted edges as well. I think the sobel and HLS thresholding parameters should be adaptively changed based on the number of peaks that we are observing in the hostogram. The parameters should be automatically adjusted in a way to obtain only two strong peaks in the histogram
