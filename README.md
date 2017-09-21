## Advanced Lane Finding
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


In this project, my goal was to write a software pipeline to identify the lane boundaries in a video and a detailed writeup of the project.  Please find the [writeup report](https://github.com/akashmod/Self_Driving_Car_Advanced_Lane_Finding/blob/master/writeup_report.md) for the project. 

The Writeup:
---
The writeup includes the [rubric points](https://review.udacity.com/#!/rubrics/571/view) as well as my description of how I addressed each point.  I have included a detailed description of the code used in each step (with line-number references and code snippets where necessary), and links to other supporting documents or external references.  I have also included images in the writeup to demonstrate how my code works with examples.   

The Project
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

The images for camera calibration are stored in the folder called `camera_cal`.  The images in `test_images` are for testing the pipeline on single frames.
The output from each stage of the pipeline is saved in the folder called `output_images`. There is a description in the writeup for the project of what each image shows.    The video called `project_video.mp4` is the video the pipeline has been tested on and have found to be working well on.  The video called `final_output_video.mp4` is the output of the `project_video.mp4` after passing through the pipeline.

Usage:
---
The [project code](https://github.com/akashmod/Self_Driving_Car_Advanced_Lane_Finding/blob/master/Advanced_Lane_Finding_P4.ipynb) can be accessed using Jupyter Notebook. The code has already been written to take in the `project_video.mp4` and process it for the output
