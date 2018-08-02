# **Finding Lane Lines on the Road** 
---

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road


[//]: # (Image References)

[image1]: ./test_images_output/ForWriteup/solidWhiteCurve-Gray.jpg "Gray"
[image2]: ./test_images_output/ForWriteup/solidWhiteCurve-Edges.jpg "Edges"
[image3]: ./test_images_output/ForWriteup/solidWhiteCurve-masked_edges.jpg "Masked_Edges"
[image4]: ./test_images_output/ForWriteup/solidWhiteCurve-lines_weighted.jpg "Lines_Weighted"
[image5]: ./test_images_output/solidWhiteRight.jpg "solidWhiteRight"
[image6]: ./test_images_output/solidYellowCurve.jpg "solidYellowCurve"
[image7]: ./test_images_output/solidYellowCurve2.jpg "solidYellowCurve2"
[image8]: ./test_images_output/solidYellowLeft.jpg "solidYellowLeft"
[image9]: ./test_images_output/whiteCarLaneSwitch.jpg "whiteCarLaneSwitch"


---

### Project Code
The project code is given in [P1.ipynb](https://github.com/anammy/CarND-LaneLines-P1/blob/master/P1.ipynb).

### Reflection

### 1. Describe the lane identification pipeline

The pipeline for extracting the lane lines consisted of 7 steps as outlined below:

1) The image/video frame was read into the program using the function 'imread' from matplotlib.image and converted to grayscale using the function 'grayscale'.
![alt text][image1]

2) A Gaussian filter was applied to the image/video frame to smooth out noise using the function 'gaussian_blur'.

3) The function 'canny' was applied to the image/video frame to extract out the edges, regions of strong gradients, using Canny edge detection technique.
![alt text][image2]

4) A region of interest was defined based on the expected positions of the lane lines and a fixed camera location on the vehicle using the function 'region_of_interest'. The edges within this region of interest were retained. Edges outside this region of interest were discarded.
![alt text][image3]

5) Within the function 'hough_lines', Hough transform was performed on the image/video frame to detect the lane lines (i.e. points of intersection in Hough space) using the function 'cv2.HoughLinesP' from the OpenCV library.

6) The output of the Hough transform function provides a list of end points for the lines detected. The draw_lines function was modified to calculate the slopes (m) and y-intercepts of these lines and categorized as belonging to either the left or right lane if m < 0 or m > 0 respectively. In addition, slopes with an absolute value lower than a threshold value were discarded to remove spurious line detections close to the horizontal axis (i.e. lines outlining the hood of the car in the video "challenge.mp4"). An additional check was performed to ensure that the absolute values of the slopes were lesser than infinity (i.e vertical lines) since this would cause errors in the subsequent calculations. After categorization, the average slope and y-intercept were calculated for each lane and were used to calculate the endpoints of the lane lines. The y-values of the solid lane lines to be drawn were set to be equal to the limits of the region of interest. The corresponding x-values were calculated using the average slope and y-intercept of each lane line. Using these x and y values, the lane lines were drawing using 'cv2.line' function from the OpenCV library on a blank image.

7) The image with the solid lane lines are overlayed with the original image using the function 'weighted_img' to produce the following final output:
![alt text][image4]

The following were the other images processed using this pipeline:

solidWhiteRight.jpg:
![alt text][image5]

solidYellowCurve.jpg:
![alt text][image6]

solidYellowCurve2.jpg:
![alt text][image7]

solidYellowLeft.jpg:
![alt text][image8]

whiteCarLaneSwitch.jpg:
![alt text][image9]

This pipeline was then built into the function called 'process_image' and was tested on the video files 'solidWhiteRight.mp4', 'solidYellowLeft.mp4', and 'challenge.mp4'. The resulting annotated video files were saved to the 'test_videos_output' file and can also be viewed within the notebook P1.ipynb.


### 2. Potential shortcomings with the current pipeline

A shortcoming of the current pipeline is that region of interest is statically defined and cannot handle sharp bends in the lane lines. For example, there are sharp bends in the lane lines in the output video of the challenge ("challenge.mp4"). The region of interest was defined for the images and videos analyzed prior to the challenge video. This region of interest doesn't work well for the sharper turns in the challenge video. This was addressed in the 'draw_lines()' function by cropping the solid right lane line drawn if curvature is detected. However, a more robust solution would be to dynamically redefine the region of interest as a function of lane curvature.

Another shortcoming of the current pipeline is the spurious line detections from the road pavement material change in the challenge video that couldn't be filtered out by increasing the threshold limits set on the slope without incurring errors. This was addressed by checking whether the vectors formed by the end points of the lane lines calculated in the current iteration where roughly collinear with the ones from the previous iteration. If this condition failed, then the lane line calculation from the current iteration is discarded and the calculated line from the previous iteration is used.


### 3. Improvements to the lane identification pipeline

An improvement to this pipeline would include a way to dynamically change the limits of the region of interest based on the sharpness of the lane curvature. Another potential improvement is to fit a polynomial of varying degrees depending on the lane curvature instead of a straight line.
