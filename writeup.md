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

[image1]: ./camera_cal_output/calibration1.jpg "Undistorted"
[image2]: ./output_images/undist.png "Road Transformed"
[image3]: ./output_images/binary.png "Binary Example"
[image4]: ./output_images/exp.png "Warp Example"
[image5]: ./output_images/download.png "Fit Visual"
[image6]: ./output_images/test1.jpg "Output"
[video1]: ./output_video/project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it! Caution, Most of my contrib is in the jupyter notebook in the examples folder, so I recommend to open it now. 

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./examples/example.ipynb" (or in lines # through # of the file called `some_file.py`).  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

the rest of calibrate image can be viewed in camera_cal_output flolder.

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

with the camera calibration and distortion coefficients computed in the first step, we can now use cv2.undistort() to correct the image, I wrote a function called distortion_correct() for pipeline,it takes an image as input and output a distortion-corrected image. 

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination thresholds in different color space to generate a binary image (thresholding steps at Basic Function cell, a function called multi_thresh()). 
I get this idea from a blog and here is the reference:
https://www.github.com/peter-moran/highway-lane-tracker 
Here's an example of my output for this step.  

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perspective_transform()`, which appears in Basic Function cell.  The `perspective_transform()` function takes as inputs an image (`img`), then output a transformed image.  I tried to find 4 source point on the original image on the lane line, and transform them in a square, since the lane line in original image seems paralell.

```python
def perspective_transform(img):
    src = np.float32(
        [
            [583,460],
            [700,460],

            [1045,680],
            [260,680],
        ])
    dst = np.float32(
        [
             [200,100],
             [1080,100],
             [1080,720],
             [200,720]

        ])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 583, 460      | 200, 100        | 
| 700, 460      | 1080, 100      |
| 1045, 680     | 1080, 720      |
| 260, 680      | 200, 720        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I tried Sliding Window Search to fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in a function called curve() and I wire a demo in Compute Curvature&Position Demo Cell, the curvatue is very large on straight line, which seems right.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in pipline1().  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

![alt text][video1]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Well my pipelines works well on straight road, but it seems failed on dark road or the road have other lines will distract my model, also, great curvatue seems fail too, I think I maytry cvEqualizeHist() for dark road, identify the width of the line(the lane line seems to be wider) or it brightness or color, to identify which line is the lane line, which one is not. Hmm.. great curvature problem seems to need a better paramter. I may try these in the future.
