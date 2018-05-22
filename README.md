# **Finding Lane Lines on the Road**

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[original]: ./test_images/challengeOnBridge.jpg "Original"
[grayscale]: ./test_images_output/old/challengeOnBridge_gray.jpg "Grayscale"
[colorselect]: ./test_images_output/challengeOnBridge_color.jpg "ColorSelect"
[gray]: ./test_images_output/challengeOnBridge_gray.jpg "Gray"
[edges]: ./test_images_output/challengeOnBridge_edges.jpg "Edges"
[region]: ./test_images_output/challengeOnBridge_masked.jpg "Region"
[output]: ./test_images_output/old/challengeOnBridge.jpg "Output"
[final]: ./test_images_output/challengeOnBridge.jpg "final"

---

### Reflection

##### 1. Pipeline.

My pipeline majorly consisted of the following 8 steps and all of them were executed in a sequence as below:

A sample image which has been obtained as a screenshot from a dashcam video source has been passed through the below pipeline for processing.

![alt text][original]

* **Color Selection**
This step was introduced in the pipeline to solve the optional challenge where the original pipeline failed to detect the lanes when there was a change in contrast of the road color across frames. The main idea is to keep only the white and yellow pixels (based on an assumption that most lanes will be in yellow or white color) in the image and filter out the rest. This was achieved by converting the original [RGB](https://en.wikipedia.org/wiki/RGB_color_model "RGB") image to [HSL](https://en.wikipedia.org/wiki/HSL_and_HSV#HSL "HSL") color space and filter the white and yellow colors using the function `color_filter()`.

![alt text][colorselect]

* **Gray Scale Conversion**
In this step, the original image or the output from previous color-filtered image will be converted to [grayscale](https://en.wikipedia.org/wiki/Grayscale "grayscale") using the function `grayscale()` to make it easier to work, namely to reduce the number of channels to work with.

![alt text][grayscale]
![alt text][gray]

* **Gaussian Blur**
In this step, we apply [Gaussian Blur](https://en.wikipedia.org/wiki/Gaussian_blur "Gaussian Blur") (also known as Gaussian smoothing) to reduce image noise using the function `gaussian_blur()`.

* **Canny Edge Detection**
In this step, we apply [Canny Edge Detector](https://en.wikipedia.org/wiki/Canny_edge_detector "Canny Edge Detector") algorithm to detect a wide range of edges in the supplied image using the function `canny()`. While, the canny edge detector automatically applies gaussian blur, I applied gaussian blur outside of the edge detector so that I could have more freedom with the kernel parameter. After running the image through the Gaussian Blur and Canny Edge Detection functions, the image is as follows.

![alt text][edges]

* **Region Of Interest**
In this step, we create a region mask using the function `region_of_interest()` and apply to the image to select only the region where we expect to find the lane lines (we assumed that the front facing camera that took the image is mounted in a fixed position on the car, such that the lane lines will always appear in the same general region of the image).

![alt text][region]

* **Hough Transform**
In this step, we apply [Hough Transform](https://en.wikipedia.org/wiki/Hough_transform "Hough Transform") algorithm to the above image using the function `hough_lines()` to identify the lane lines within the image.

![alt text][output]

* **Lane Line Averaging**
In this step, the detected line segments from above have been filtered / averaged / extrapolated to map out the full extent of the left and right lane boundaries using the function `generate_lines()`.  This function calculates the slope and intercept of each line segment and separates the positive and negative sloped lines. Then, it averages the position of each of the lines and extrapolate to the top and bottom of the lane. In order to be more robust, the above was function was again modified to create a new function `generate_lines_advanced()` which included the removal the slope outliers based on some assumed distribution, calculation of weighted average of the slopes using their line lengths and also a running average of last 'n' lines for the left and right lanes across frames to smoothen out the drawing.

* **Draw Lines**
In this final step, we draw the above lane lines on to the original image using the function `draw_lines()`.

![alt text][final]

The pipeline has also been applied to the below videos and the results were satisfactory:

 [![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/Egf5MbOSAeI/0.jpg)](https://www.youtube.com/watch?v=Egf5MbOSAeI)
 
 [![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/HYhDVlUXeZ0/0.jpg)](https://www.youtube.com/watch?v=HYhDVlUXeZ0)
 
 [![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/AjK2Rx5Uozg/0.jpg)](https://www.youtube.com/watch?v=AjK2Rx5Uozg) 

##### 2. Shortcomings

While the pipeline showed amazing results when applied to the images which contains straighter road lanes, it struggles when the road lanes are as below:
- Road lanes which are not yellow or white or under poor lighting conditions.
- Road lanes which has more curves.
- When the camera angle is more more steeper than normal.

##### 3. Improvement suggestions

Some of the possible improvement suggestions are as below:
- When the road lanes are not yellow or white or under poor lighting conditions we can have a fallback to have a dynamic region of interest calculation based on the location of  usual lanes and not the entire region. It would eliminate detection of unnecessary edges in the middle of the road.
- We can try to apply the classical Hough technique for curve detection when we encounter more curved lanes on the road.
- We can improve the algorithm to track the horizon and use it to compensate when the roads are much steeper than usual.
