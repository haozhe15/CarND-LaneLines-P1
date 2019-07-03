# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # "Image References"

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps.

1. Convert the images to grayscale
2. Define a kernel size and and apply Gaussian smoothing.
3. Define parameters for Canny and apply canny edge detection
4. Create a masked edges image. Here I use ratio of image height and width to define the positions of vertices of the mask. The lower edge of the mask is a little higher than the image bottom  (y = 0.92*height = 500) to prevent detecting irrelevant edges.
5. Define the Hough transform parameters and do the hough transform
6. Add the line image to the original image to produce the result image
7. Show and Save the image

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by doing these:

1. Create two list called left and right to store valid lane line segments on the left and right. Iterate each line, Use `atan2(y2-y1,x2-x1)`to calculate the angle between the line segment and x-coordinate in degree. If angle <= -20 degree then add it to the left lists, if angle >= 20 degree then add it to the right lists.

2. For each line in a list (either left or right), I want to extend it to the lower edge and the horizon position in the image and record the corresponding x coordinates. I do this by the expressions below:
   $$
   lower\_x = \frac{(lower\_y-y_1)\cdot(x_2-x_1)}{y_2-y_1} + x_1\\
   upper\_x = \frac{(upper\_y-y_1)\cdot(x_2-x_1)}{y_2-y_1} + x_1
   $$
   Here lower_y is image height and upper_y is 0.6 times image height.

   Append all lower_x into a list and calculate their avarage as the final estimation of the lower_x. If there are no valid line in the list, lower_x is `None`. Do the same for the upper_x value.

3. Draw left and right lines on the image with corresponding lower_x, lower_y, upper_x and upper_y. Note that lines are only drawn when lower_x and upper_x are not `None`.


### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming would be what would happen when the car isn't moving forward in the middle of the road, since the position of mask is fixed, some lane lines could be outside of the mask and be missed out and irrelavent lines could be covered.

Another shortcoming could happen when line segments irrelavent to the lane are detected and add to the left or right list, the estimation of the lane prediction will be inaccurate.

Third shortcoming is that if the car is turning and the lane lines are not straight, the pipeline can't work well.

Forth shorcoming is that if there are shadows in the environment, the edges of lanes are hard to get by only using canny edge detection.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to make the position of the mask dynamic, so it will always cover regions of interest.

Another potential improvement could be: when average and extropolate lines, discard outliers and only choose those lines with simmilar slope and intercepts.

Other improvents could be utilizing more advanced computer vision techniques.

