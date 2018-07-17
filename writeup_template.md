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

My pipeline consists of 7 steps
1.  convert the input-image to grayscale, using "grayscale()"
2.  apply Gaussian smoothing, using "gaussian_blur()", kernel_size=7
3.  apply Canny algorithm as in lecture, using "canny()"
4.  clip the image to a polygon (isosceles trapezium), using "region_of_interest()"
5.  apply Hough transform using, using "hough_lines()", yielding an image with a pair of (lane) lines.
6.  clip the image again, using "region_of_interest()" once more. Otherwise the aforementioned lines would form an X-shaped "cross". 
7.  blend the image of the clipped lane-lines with the original input-image, using "weighted_img()", and return
 

 
In order to find a single line representing the left lane and another representing drawthe right lane, I modified the "draw_lines()" function as follows:
* partition the list of lines provided by the Hough transform  into "left-line-sublist", "right-lines-sublist" and "rubbish-lines-sublist". Detailed criteria for filtering see code.
* compute offsets and slope for each line in those candidate-sublists (effectively performing a Hough back-transform, manually ...)
* average over "left-lines-sublist" and "right-lines-sublist", to get the parameters of a new estimate for left-line and a right line. This may fail!
* output a flag indicating if the aforementioned worked or failed.
* using a global variable  "prev_line_image" (yes I know, see comments/suggestions below, this is nothing but a quick and truely dirty hack ... but it works for a first prototype) I provide a fallback image of lines. 

The following figure 
![alt text][image2]
and the following link to the
["challenge" video with lane-lines](./test_videos_output/challenge.mp4)
seem to demonstrate that the implementation submitted does the job, i.e. it should meet specification, at least "Proof of Concept" ...

Yet it has several shortcomings both in function and in programming style, see detailed discussion of shortcomings and suggestions for improvements below.

### 2. Identify potential shortcomings with your current pipeline

1. Safety risk: If no new lane-lines can be identified from the present picture, the last set of lane-lines is used. This may go on for an indefinite period of time. This makes the image flicker-free in the good case (lines are physically there and where they should be, and we merely experience occasional drop-outs), but in the bad case (e.g. car if off the road ...) pretending to see lines where there (possibly) aren't any lines is clearly not a good idea from a safety perspective!

2. The use of a global variable for storing the last set of lines is very bad programming style.

3. In order to avoid "wobbling" one might want to implement some kind of moving average 

4.  The implementation is not scale invariant (I had to make an ad hoc correction for the third video)

5.  Computing slope and offset "by hand" is ineffective, as it is effectively a back-transformation to Hough space and that is where we just came from ... 

6. The following shortcoming has been fixed: Dropouts, occasionally no lines where identified (btw the default return (0,0,0,0) in the case of failing to find a new set of lane-lindes is clearly suboptimal ...)

7. The following shortcoming has also been fixed: In the most difficult "challenge" video there are other lines with substantial slopes (>0.1 or < -0.1) that were initially mistaken for lane-line-candidates distorting the averaging procedure. 


### 3. Suggest possible improvements to your pipeline

1. Concerning the safety risk, after a few milliseconds without a valid new estimate, a warning should be issued, since this is tantamout to sensor failure. This warning may be used by the overall system to take appropriate actions. 

2. Redesign in order to get rid of the global variable: Introduce a class "lane_lines_estimator" with 2x4 coordinates representing the current estimate of the lane-lines as class-variables. Maybe it is better to use slope/offset instead. Probably also a "valid-flag" as a class-variable, plus a "time-since-last-valid-update" counter .... 
If the finding of new lane-lines in step 5 of the pipeline does not fail, these class-variables may be updated accordingly by some method, otherwise they just remain as they are. Possibly the "valid" counter gets incremented instead. The rendering step 6 of the pipeline may collect the current estimate from an instantiation of that class using an appropriate method. 

3. Possibly the problem with different image-shapes will disappear anyway, as soon as we get rid of the global variable storing the last valid set of lane-lines found, as described in the above paragraph. 
Otherwise, all positional quantities must longer be expressed in absolute pixel values but relative to the shape of the image. 

4. Avoid "wobbling" by smoothing the aforementioned estimate using some kind of Moving Average technique, e.g. exponential moving average or something more complex. This may be incorporated in the aforementioned update-method of the "lane_lines_estimator"-class.fically lines further out having a smaller slope in absolute value are no longer eligible in "draw_lines()", as they were i

5. Identifiying lines with the appropriate slopes may be done in Hough-space, before transforming things back to lines in offset/slope-space (cluster in two "stripes" representing a respective neigbourhood of the desired slopes. Possibly one may be able to exploit symmetry, as the image should be roughly symmetric (as long as nothing goes wrong ..). However, this would imply going under the hood of the cv2 Hough transform functions. So maybe it is ok to leave it as it is. 

6. (The problem of dropouts/flickering has been fixed, by virtue of the "last-valid-estimate" mechanism, reprehensible as it may be at the present state because of the global variable)

7. (The problem of filtering out non-lane-line lines has been fixed, by specifying a fairly narrow range for acceptable positive and negative slopes, respectively. Specifically lines further out having a smaller slope in absolute value are no longer eligible in "draw_lines()", as they had been initially. This has been further alleviated by introducing the aforementioned "last-valid-estimate" mechanism, since this allowed for tighter clipping. The tighter the clipping gets, the more temporary dropouts will occur, as fragments of non-contiguous lines will scroll out of the picture every now and then. )



