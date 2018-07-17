# **Finding Lane Lines on the Road** 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

The implementation does the job, i.e. should meet specification, yet it has several shortcomings both in function and in programming style, see details and suggestions for improvements below.

[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consistsof 7 steps
*  convert the input-image to grayscale, using "grayscale()"
*  apply Gaussian smoothing, using "gaussian_blur()", kernel_size=7
*  apply Canny algorithm as in lecture, using "canny()"
*  clip the image to a polygon (isosceles trapezium), using "region_of_interest()"
*  apply Hough transform using, using "hough_lines", yielding an image with a pair of (lane) lines.
*  clip the image again, using "region_of_interest()" once more. Otherwise the aforementioned lines would appear too long and form an X-shaped "cross". 
*  blend the image of the cliped lane-lines with the original input-image, using "weighted_img()", and return
 
In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
