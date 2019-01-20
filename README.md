## Advanced Lane Finding

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
[image0]: ./output_images/undistorted_img.png "Undistorted Image"
[image1]: ./output_images/sobel_binary_image.png "Gradient X image"
[image2]: ./output_images/color_thresholds_binary_image.png "HLS Binary Image"
[image3]: ./output_images/main_binary_image.png "Main Binary Image"
[image4]: ./output_images/warped_binary_image.png "Warped Binary Image"
[image5]: ./output_images/warped_img.png "Warped Image"
[image6]: ./output_images/histogram_updated_image.png "Histogram Image"
[image7]: ./output_images/final_image.png "Final Image"
[image8]: ./examples/curvature_radius_calculation.png "Radis of Curvature Calculation Formula"
[image9]: ./camera_cal/calibration1.jpg "Example distorted Chess board image"
[image10]: ./output_images/calibration1_undistorted.png "Example undistorted chess board image"
[video1]: ./project_video.mp4 "Video"



### Camera Calibration

The code for this step is contained in the first code cell of the IPython notebook located in "./P2.ipynb".

I started by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.

As an example, I used the camera calibration and distortion coefficients to undistort the image: 
![alt text][image9]

After undistortion, image looked like:

![alt text][image10]

### Pipeline (single images)

#### 1. Distortion Correction

The images for camera calibration are stored in the folder called camera_cal. I compute the camera matrix and distortion co-efficients to undistort the image. After applying the distortion correction, test images looked like this one:

![alt text][image0]

#### 2. Creation of thresholded binary image

First I created binary image by applying gradient X thresholds as shown below:
![alt text][image1]

Then, I created binary image after applying HLS and HSV color thresholds to detect yellow and white lane colors as shown below:
![alt text][image2]

Then I OR'ed above binary images as shown below:
![alt text][image3]

#### 3. Perspective Transform

After applying perspective transform, here is how binary image looked like:

![alt text][image4]

After applying perspective transform, here is how test image looked like:

![alt text][image5]

#### 4. Identification of lane-line pixels

I then performed a sliding window search on the bottom half (as lanes are in bottom half portion throughout) of the binary image obtained in above step, starting with the base likely positions of the 2 lanes, calculated from the histogram. I have used 9 windows of width 100 pixels.

Histogram image is shown as:
![alt text][image6]

The x & y coordinates of non zeros pixels are found, a polynomial is fit for these coordinates and the lane lines are drawn.

![alt text][image7]

#### 5. Calculation of Radius of Curvature and Centre Offset

I calculated radius of curvature inside function measure_curvature. Mathematical formula I used for calculation of radius of curvature is:

![alt text][image8]

Also, I calculated centre offset inside function measure_centre_offset by calculating distance between centre of lane and centre of image.

Radius of Curvature and Centre Offset is shown in the output video as:

![alt text][image7]

### Pipeline (video)

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Points of failure & Areas of Improvement

Improvements required:
1. If there are sharp turns, then perspective transform will keep some of the lane pixels out of the histogram image and thus lane lines cannot be drawn accurately. So improving the logic of getting histogram image with all the lane pixels after perspective transform may help.
2. Improvement in bit mask extraction for detection of lane pixels which are not bright (white).
3. Checking if radius of curvature of the two polynomials is almost same or not.

Failures:

The pipeline seems to fail for the harder challenge video. This video has sharper turns and at very short intervals. Maybe, polynomials of higher degree (maybe 3) can help in drawing lane lines. In this case, calculation of radius of curvature will be difficult due to complexity involved in calculating derivative of the polynomial.

