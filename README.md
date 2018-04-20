# **Finding Lane Lines on the Road**
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />

Overview
---

When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

In this project we will detect lane lines in images using Python and OpenCV. OpenCV means "Open-Source Computer Vision", which is a package that has many useful tools for analyzing images.


The Project
---

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road


[//]: # (Image References)

[image1]: ./test_images/solidWhiteRight.jpg "Source image"
[image2]: ./test_images_output/gray.jpg "Gray image"
[image3]: ./test_images_output/blurred.jpg "Blurred image"
[image4]: ./test_images_output/canny.jpg "after applying Canny edge detection"
[image5]: ./test_images_output/mask.jpg "Masked image"
[image6]: ./test_images_output/hough.jpg "after applying Hough line transform"
[image7]: ./test_images_output/final.jpg "Final image"

---


### Pipeline

My pipeline primarily consists of the following steps. The function `process_image()` implements the pipeline.

#### Image processing
* First, I read the original image with shape `(540, 960, 3)`. An image looks as follows.

![alt text][image1]

* Then I convert it to gray scale image using `cv2.cvtColor(img, cv2.COLOR_RGB2GRAY)`. It looks as follows.

![alt text][image2]

* Then I applied a smoothening technique on it called `Gaussian blurring` using `cv2.GaussianBlur()` and 5 as kernel size.

![alt text][image3]

#### Edge detection
* Next, I applied `Canny's edge detection` algorithm on the grayed out image to find all possible edges using `cv2.Canny()`. I used 50 as the low threshold and 150 as high threshold for this purpose.

![alt text][image4]

* Since the above algorithm finds other edges as well along with the edges on the lane, I used a trapezoidal mask to select the edges only around the lane. The size of the trapezoid as follows:
```python
bottom_left = (135, 960)
top_left = (400, 335)
top_right = (565, 335)
bottom_right = (920, 960)
```

![alt text][image5]

#### Hough line transform
* Then I apply `Hough line transform` algorithm on the masked image to approximate lines (by extrapolation, see function `extrapolate_lane_lines()`) from several edges obtained from Canny's algorithm. This results in only two sets of lines, one for the left lane and the other for the right lane. I used `cv2.HoughLinesP()` for this purpose with the following parameters.
```python
rho = 1                 # distance resolution in pixels of the Hough grid
theta = np.pi / 180     # angular resolution in radians of the Hough grid
threshold = 35          # minimum number of votes (intersections in Hough grid cell)
min_line_len = 10       # minimum number of pixels making up a line
max_line_gap = 25       # maximum gap in pixels between connectable line segments
slope_threshold = 0.5
```

![alt text][image6]

#### Drawing detected lane lines
* Our final task is to transform the left set of lines to a `single left line` and the right set of lines to a `single right line`. I do this for each lane in the `draw_lines()` function as follows:
    * I discard any lines with slope close to 0 (represents horizontal lines)
    * Then I took the mean of x and y coordinates and the gradient of the lines, which will help us provide a unique line. With the help of a bookkeeping variable, I found the topmost y value for the lines and find the corresponding x using the above line equation.
    * Assuming the line touches the bottom end of the image, we get another set of points. This gives us a single line.

![alt text][image7]


### Discussions

* The pipeline still does not work with the challenge video correctly. This possibly needs few additional processing to the source image to identify other noise and remove them before applying Canny's algorithm.
* I assumed the lane lines end to the bottom end of the image to find the second set of points. This is not true for the challenge video and need to be addressed.
* Of course, I do not take care of other real-world noises on image like rain, shadow, people, etc. or other possible lines on the road (like, x signs and zebra crossing, left only sign, etc), or sharp turns.

and many many more...This is just the beginning :)


### References

Primarily the following three.
* Medium posts about self-driving
* Computer vision udacity lecture and other course provided resource
* sdc-nd slack channel

