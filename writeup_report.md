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

[image1]: ./examples/undistort_input.jpg "Original Image"
[image2]: ./examples/undistort_output.jpg "Undistorted"
[image3]: ./examples/binary_comb_output.jpg "Binary Example"
[image4]: ./examples/pers_trans_source.jpg "Source Image for Warp"
[image5]: ./examples/pers_trans_output.jpg "Warped Output"
[image6]: ./examples/polynomial_fit_output.jpg "Polynomial Fit Output"
[image7]: ./examples/rcurve.jpg ""
[image8]: ./examples/final_output.jpg "Final Output"
[video1]: ./final_output_video.mp4 "Final Output Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

### Camera Calibration

#### 1. Computation of the camera matrix and distortion coefficients with an example of a distortion corrected calibration image.

The code for this step is contained in the third code cell of the IPython notebook located in the main directory named Advanced_Lane_Finding_P4.ipynb.  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function to the image shown below.

![alt text][image1]

On applying the undistort function, the image shown above was undistorted and the resulting image is shown below.

![alt text][image2]

### Pipeline (single images)

#### 2. Use of color transforms and gradients to create the thresholded binary image.

I used a combination of color and gradient thresholds to generate a binary image, the functions for both can be found at the cell number 4 of the Advanced_Lane_Finding_P4.ipynb. The gradient threshold was only performed along the x-axis to detect the lane lines since they are rather vertical in the image. The color threshold was performed in the HLS domain. The image was converted to HLS and the threshold was placed only on the S (saturation) layer, which helped detect lane lines better. Here's an example of my output for this step after combining both color and gradient threshold.  (note: this is not actually from one of the test images)

![alt text][image3]

#### 3. Perspective Transform

The code for my perspective transform includes a function called `perspective_transform()`, which appears in cell 5 of the file `Advanced_Lane_Finding_P4.py`.  The `perspective_transform()` function only takes as inputs an image (`image`). The source (`src`) and destination (`dst`) points are defined in the function itself. The function takes the source points in the image and tries to map it in the destination points and hence performing a perspective transform of the image.  I took a sample image and manually chose the points in the image as the source points and then manually selected the destination points. These source and destination points would then serve for all images input into the program. I then hardcoded the source and destination points in the following manner:

```python
src=np.float32([[207,720],[575,459],[711,459],[1103,720]])
dest=np.float32([[300,720],[300,0],[900,0],[900,720]])

```

The following table shows the source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 207, 720      | 300, 720      | 
| 575, 459      | 300, 0        |
| 711, 459      | 900, 0        |
| 1103, 720     | 900, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image. The figure below shows the points in the source image used as source points.

![alt text][image4]

The figure below shows the perspective transformed image. Since the function takes in only grayscale images with a single layer and also outputs grayscale images, the output figure shown below is also in grayscale.

![alt text][image5]

#### 4. Identification of lane-line pixels and polynomial fitting.

The next step to fit the polynomials to the lines is an elaborate step. There are two parts to this: Using the previous line information to fit a new line and Window Search. Using the previous line information is what I like to call plan A, as it is the first method that we will try to use to detect lanes and fit the line. If the algorithm is unable to find a line, then the pipeline uses the window search algorithm, hence plan B. 

To use the previous line information, we take the previous left lane and right lane line coefficients and apply them to the new nonzero points in the binary output image with a margin of pixels. The idea is if the new line lies around the margin of the previous line polynomial, it is probably our new lane lines. The pipeline takes in these values and stores them in an array and then tries to fit a polynomial line. The coefficients of the new line formed are then stored in an array and now this line is used to detect lanes for the next image.
However, for the first image that the pipeline takes in, it has no information for the previous line. There is also a possibility that the lane lines may be present in the image but not within the margin of the polynomial function of the lines of the previous image. In such cases, the pipeline takes help of the window search method. The method uses the histogram function to get the histogram plot of the sum of the columns of the lower half of the image. The horizontal axis values where the peaks are detected are safely assumed to be the starting points of the lane lines in the image. The algorithm next divides the image into a set of windows, particularly 9 windows. It searches the next window within the margin defined, that is, 100 pixels around the histogram peak. As soon as pixels are detected, they are appended to an array and the mean of the pixels of this window serves as the reference point for the next window. The pipeline again searches for pixels around the reference point within the margin of 100 pixels. Once the entire image is scanned, the appended lists of left and right lanes are used to fit a polynomial.Figure shows an example of a polynomial fit in an image:

![alt text][image6]

#### 5. Calculation of the radius of curvature of the lane and the position of the vehicle with respect to center.

![alt text][image7] 
The calculation of curvature of the lane lines was done in the last cell in the python notebook Advanced_Lane_Finding_P4.ipynb. I have used the formula shown above for the calculation of the lanes lines on the lane pixels closest to the vehicle for both the left lane and the right lane. The mean of the two values denoted the actual radius of curvature of the vehicle. The position of the vehicle was calculated by calculating the midpoint of the lane and subtracting it from 640, i.e. half of the horizontal size of the image. The value found in pixels was converted to m.

#### 6. The Final Image.

The final image was plotted using matplotlib library and was seen to clearly show the marked lane lines, the radius of curvature and the position of the vehicle from the center. 

![alt text][image8]

---

### Pipeline (video)

#### The Link to the Final Video

Here's a [link to my video result](./final_output_video.mp4)

---

### Discussion

#### Challenges faced in the project and future work.

The most challenging areas in the project for me was the thresholding of the source image to create a binary image. The thresholding had to be done in such a way that the lane lines were detected effectively and the least amount of noise was involved from the surrounding objects in the image. After several iterations, I finally arrived at the sobel x gradient thresholding and the color saturation thresholding to be used for the images. 

Although the pipeline seems robust and has worked well for the video, there are several improvements that can be made in the model to improve its robustness. The model, as of now, finds it difficult to differentiate between the shadows and the lane lines. This may cause the car to wrongly detect the shadow lines as the lane lines. Once the shadow lines are detected as lane lines, the same lines would then be used for the next image as the model uses information of the previous lane lines to calculate the new lane lines. Hence, the new lane lines would also most likely be detected wrongly and hence can cause the car to drive off the road. However, this problem can be resolved through better thresholding while producing the binary image is the next step of improvement in the project.
