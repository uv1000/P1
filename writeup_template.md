# **Finding Lane Lines on the Road** 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./test_images/output/solidWhiteCurvewith_lines.jpg "Static example image with lines"


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
 

 
In order to draw a single line on the left and right lanes, I modified the draw_lines() function as follows:
* partition the list of lines provided by the Hough transform  into "left-line-sublist", "right-lines-sublist" and "rubbish-lines-sublist"
* compute offsets and slope (effectively performing Hough back-transform manually ...)
* average over  "left-lines-sublist" and "right-lines-sublist", to get a left-line and a right line. This may fail!
* output a flag indication if the aforementioned worked or failed.
* using a global variable  "prev_line_image" (yes I know, see comments/suggestions below, this is nothing but a quick and truely dirty hack ... but it works for a first prototype) 

The following figure 
![alt text][image2]
and the following link to the
["challenge" video with lane-lines](./test_videos_output/challenge.mp4)
should demonstrate that the implementation submitted does the job, i.e. it should meet specification. Proof of concept

Yet it has several shortcomings both in function and in programming style, see detailed discussion of shortcomings and suggestions for improvements below.

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
