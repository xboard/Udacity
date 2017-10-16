# **Finding Lane Lines on the Road** 


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image0]: ./examples/grayscale.jpg "Grayscale"
[image1]: ./test_images_output/range_solidWhiteRight.jpg "White and yellow color ranges"
[image2]: ./test_images_output/gray_solidWhiteRight.jpg  "One channel / grayscale"
[image3]: ./test_images_output/gb_solidWhiteRight.jpg    "Gaussian blur filter"
[image4]: ./test_images_output/canny_solidWhiteRight.jpg "Canny edge detection"
[image5]: ./test_images_output/roi_solidWhiteRight.jpg   "ROI filter"
[image6]: ./test_images_output/hl_solidWhiteRight.jpg "Hough Lines"
[image7]: ./test_images_output/hll_solidWhiteRight.jpg "Land Lines"
[image8]: ./test_images_output/wgt_solidWhiteRight.jpg "Land Lines in image"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 8 steps:
1. Apply a filter on the image to select only yellow and white pixels in a range. The idea is to reduce noise in the image trying to select only pixels that may be land lines;
![alt text][image1]

2. Converted the images to grayscale;
![alt text][image2]

3. Applied a gaussian blur filter to help reduce noise;
![alt text][image3]

4. Detected edges using the canny algorithm;
![alt text][image4]

5. Filtered out everything out of a ROI, region of interest (a trapezoidal region);
![alt text][image5]

6. Detected the hough lines for each lane;
![alt text][image6]

7. Filtered out outliers, averaged and extrapolated the rough lines for each lane;
![alt text][image7]

8. Combined the image with the rough lines with the original image.
![alt text][image8]


In order to draw a single line on the left and right lanes, I created a new function called `draw_xavg_lines`. It receives all lines deteced through the
hough lines algorithm (previous step) and split them in two groups, one with positive and the other with negative line slopes. For each one of this groups
every line segment that is more than `threshold` standard deviations away from the mean is discarded and the remaining contribute with a set of points that is
passed to `cv2.lineFit` to get the parameters to the line that best fits all the points. This line is then extrapolated to go from the botton of the image up to
the top of the ROI, but preserving the fitted slope.
 
If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when a the car is in a turn/curve or if the road don't have maintenance for a long time and the land lines are barely visible.

Another potential shortcoming would be if the road have white or yellow signs painted between the lanes like [this](http://c8.alamy.com/comp/DXHPJ2/30-mph-speed-limit-sign-painted-on-road-DXHPJ2.jpg) or worst: someone may deliberately draw yellow or white lines in the road to confuse the self driving car land line detection algorithm.

Another shortcoming would be if the road have ups and downs, at the peak/bottom the land lines may be invisible for the camera for a very long time.



### 3. Suggest possible improvements to your pipeline

This implementation is memoryless, that is, it doesn't use information between frames. This certainly would help to give more stability in the land lines
detections since it's very unlikely that the landline may have shifted abruptly between two consecutive frames.

