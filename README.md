# **Finding Lane Lines on the Road** 

## Writeup Submission

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/solidWhiteCurve.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. 
1. I converted the images to grayscale.

2. I applied Gaussian smoothing to this image with a kernel size of 5 in order to improve the subsequent edge detection.

3. I applied the Canny transform to find edges in the greyscale image.

4. I created a masked image to focus on the lower area of the image containing the main lane lines as the region of interest, and ignore the remainder of the image.  I found a height of 325 worked well as the cut-off for the lane into the distance, and limiting the upper boundaries to 450 and 550 respectively to only see the current lane into the distance.

5. I applied the Hough transform in order to transform the edges detected in this masked region into a series of lines.  I experimented with values to get a reliable detection of the lane lines and found that a rho of 1, theta of np.pi/180 radians, a threshold of 15, max_line_length of 10 and a max_line_gap of 5 gave good results on both static and video outputs.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by:
1. Calculating the slope and centre for each line that the Hough transform output

2. Assigning these to either a left or right list based on their slope being positive or negative.  I also applied a lower threshold of 0.2 (or -0.2 for the right slope) and an upper threshold of 0.8 (or -0.8 for the right slope) in order to exclude lines which were too horizontal or vertical and likely to negatively affect the lane line detection.  Without doing this, I found the line would occasionally flicker to a skewed angle outward or inward in some parts of the resulting video output.

3. Calculating the average slope and centres of all of the lines assigned to the left and right lists

4. Calculating the new x coordinates of the resulting two lines based on our knowledge of the centre coordinates, the slope and the y' being either the bottom of the image, or the height of the masked image (325 in my case)

After these changes, the output looked as follows for a static image:

![example output][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the road curved too much in the input video or image, causing it to go outside the masked area or for the 'averaged' line to cease to be representative of the actual lane lines on the road.

Another shortcoming could be that the use of a straight line and fixed height line is not appropriate where the image contains a right or left hand turn or an intersection without lane lines.

Our pipeline is also likely to experience problems in poor light conditions, on surfaces where there are a lot of other markings on the road which edge-detection would find, or in situations where the image from the camera has different orientations making the masking and line heights incorrect.

Finally, if a vehicle were in the lane ahead, our pipeline would probably get confused by its edges and introduce noise!


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to try to create the masked area based on where lane lines/edges are detected in key frames or areas of the image, rather than using a fixed mask.

Another potential improvement could be to split the output Hough lines into segments up the image so that you draw several lines along the detected edges to allow for curves or other variations in the lane.

Finally, doing semantic segmentation on the image before processing would perhaps allow us to ignore known items like other cars so that we don't introduce that noise into our lane line detection.
