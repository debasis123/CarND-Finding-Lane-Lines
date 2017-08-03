# **Finding Lane Lines on the Road**

<!-- ## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.


---
**Finding Lane Lines on the Road**
 -->


The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images/solidWhiteCurve.jpg "Source image"
[image2]: ./test_images_output/solidWhiteCurve.jpg "Final image"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consists of 5 steps.

1. First, I read the original image (with 3 channels) and converted it to gray scale image using a smoothening technique called Gaussian blurring.
2. Next, I applied Canny's edge detection algorithm on the grayed out image to find all possible edges.
3. Since the above algorithm finds other edges as well along with the edges on the lane, I used a trapezoid mask to select the edges only around the lane.
4. Then I apply Hough line transform algorithm on the masked image to approximate lines from several edges obtained from Canny. This gives us two sets of lines, one for the left lane and the other for the right lane.
5. Our final task is to transform the left set of lines to a single left line and the right set of lines to a single right line. I do this for each side of the lane in the draw_lines() function as follows:
    * I discard any lines with slope close to 0 (represents horizontal lines)
    * Then I took the mean of x and y coordinates and the gradient of the lines, which gives us a unique line. With the help of a bookkeeping variable I found the topmost y value for the lines and find the corresponding x using the above line equation.
    * Assuming the line touches the bottom end of the image, we get another set of points. This gives us a single line.

The first image below is an input image and the red lines on the next image indicates our final pair of lines.

![Source image][image1]
![Final image with two lines describing ends of the lane][image2]

### 2. Identify potential shortcomings with your current pipeline

1. The pipeline still does not work with the challenge video correctly. This possibly needs few additional tweaking to the source image to identify other noise and remove them before applying Canny.
2. I assumed the lane lines end to the bottom end of to find the second set of points. This is not true for the challenge video and need to be addressed.
3. Does not take care of other noise on the image like rain, shadow, people, etc. or other possible lines on the road (like, x signs and zebra crossing, left only sign, etc), or sharp turns

and many many more...I plan to improve it later again.


### 3. Suggest possible improvements to your pipeline

1. Better noise reduction to the input image to extract out only the intended lane
2. Design our own Canny and Hough transform to cater to our specific needs
3. Machine learning to cancel out noise or predict the next extension of lines where the lane borders are too dim or too separated.

### References
Primarily the following three.

1. Medium posts about self-driving
2. Computer vision udacity lecture and other course provided resource
3. sdc-nd slack channel
