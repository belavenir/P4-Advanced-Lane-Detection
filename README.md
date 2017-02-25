##Advanced Lane Lines Detection

All the code is written in the IPython notebook located in "./project4.ipynb".

---

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

[image1]: ./output_images/undistort_output.png "Undistorted"
[image2]: ./output_images/example.png "Road Transformed"
[image3]: ./output_images/binary_combo_example.png "Binary Example"
[image4]: ./output_images/warped_straight_lines.png "Warp Example"
[image5]: ./output_images/histogram.png "Fit Visual"
[image6]: ./output_images/color_fit_lines.png "Sliding window"
[image7]: ./output_images/output1.png "Output1"
[image8]: ./output_images/output2.png "Output2"
[image9]: ./output_images/output3.png "Output3"
[image10]: ./output_images/output4.png "Output4"
[image11]: ./output_images/output5.png "Output5"
[image12]: ./output_images/output6.png "Output6"
[video1]: ./result.mp4 "Video"


###Camera Calibration

It is my first step to avoid distortions in images.

The code for this step is contained in the third code cell of the IPython notebook.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Lane Detection Pipeline

####1. Undistort a test image
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

####2. A cthreshold-combined binary image transforms
I used a combination of color and gradient thresholds to generate a binary image. The function is called 'combined_thresh',located in 9th code cell.

* Transform the olor space to HLS space and add threshold on the S channel;
* Use sobel operator in horizontal, vertical to get x/y gradients and its magnitude and direction;
* Combined all the above threshold to generate the final binary image.

Here is an example of my output for this step. 

![alt text][image3]

####3. Perspective transformat

The code for my perspective transform includes a function called `warp()`, which appears in the 10th code cell of the IPython notebook).  The `warp()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points and gets outputs of warped image and an inverse transformation matrix.

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

####4. Identify lane-line pixels and fit with a polynomial

Then I did the same way proposed in the oneline course:
* Line finding by peaks in a histogram of a low_half image;

![alt text][image5]

* Implement sliding windows and fit a lane lines with a 2nd order polynomial. 

![alt text][image6]

####5. Calculate the radius of lane curvature and the position of the vehicle w.r.t center.

I did this in the same way proposed in course. The relative functions are called 'cal_curvarad' and 'cal_offset' in 17th and 18th code cell respectively.

####6. Test on example images.

I implemented this step in 21th code cell.  Here is an example of my result on a test image:

![alt text][image7]
![alt text][image8]
![alt text][image9]
![alt text][image10]
![alt text][image11]
![alt text][image12]

---

###Pipeline (video)

According to the above pipline, we transfer it to a video clips. 
Even when everything is working, the line detections will jump around from frame to frame a bit and it can be preferable to smooth over the last n frames of video to obtain a cleaner result. Each time you get a new high-confidence measurement, I append it to the list of recent measurements and then take an average over 10 past measurements to obtain the lane position I want to draw onto the image

Here's a [link to my video result](./result.mp4)

---

###Discussion

I found the pipeline does not work well in challenge & harder challenge clips maily because the lane pixel is not clear enough to test. So the histogram peak cannot have a high-confidence detection. Maybe a outlier filter should be add and the previous used polynomial area should be tracked.
