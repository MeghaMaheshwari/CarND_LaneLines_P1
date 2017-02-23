# Finding Lane Lines on the Road

The goals / steps of this project are the following:
* Detect lanes in single images and mark them
* Use this mechanism obtained for single image on a video stream and mark the lane

For example :
##### Original Image

	![Input Image][images/solidWhiteCurve.jpg]
    
##### Output Image

	![Output Image][images/route.png]
    

### Detailed Description

* The pipeline consists of seven stages and all the frames in the video run through these stages in order to generate the lane detected outout video 

My pipeline consisted of 7 steps. 

* **GrayScale Image** : First, I converted the images to grayscale as operating on grayscale images is a lot cheaper and faster than colored images as we need to deal with only one channnel. It also eliminates the dependencies on color. 
* **Guassian blurring** is than applied on this grayscale image with a kernel size of 5 to eliminate any dependency on noise.

*  **Canny edge detection** is than applied on this grayscale image. Since the lanes have a bright white intensity , an upper threshold of 200 is selected and a lower threshold of 150 has been selected to get clear edges on the white lanes.
*  **Create Masked Edge Image** A masked edge image is than created using cv2.fillPoly() function. The vertices for the ploygon region of interest have been determined after experimentation
*  **Hough Lines** The Hough lines are than run on the edge detected image
*  **Separate Left and Right Lane** The left and right lanes have to be separated so that they can be individually drawn to get clear lanes. In order to draw a single line on the left and right lanes, I modified the mechanism to draw lines.
	* First we iterate over the lines obtained by Hough Transform and obtain the slope of each line
	* If the slope is negative, the line belongs to the left side of the lane
	* If the slope is positive or zero, the line belongs to the right side of the lane
	* Once we have obtained two arrays, one containing the left line co-ordinates and the other containing the right line co-ordinates, we take an average of them
	* As we calculate the average in each frame, we keep storing the average that were obtained in previous frames.
	* The needed average value of left and right side is than determined by averaging the current value with the last 50 values of the previous average. This is needed to ensure that there is no flickering in the video.
	* Once we have the x and y co-ordinates for each of the left and right side, we obtain the slope usind the standard equation 
	   slope = (y2 - y1) / (x2 - x1)
       and intercept using the standard line equation , y = mx + c
    * From the region of interest that was defined in Hough Transform, we know the end y co-ordinates upto which we wish to draw the line. We than determine the x co-ordinates for both the left and right side using the standard line equation , y = mx + c and the lines are drawn.
    * The lines thus obtained are than drawn on the original image using the addWeighted function.
       



If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


#### 2. Potential shortcomings with the current pipeline


* The current pipeline will not give good results on curved roads
* Also the average mechanism that has been used to store the average of previous frames and than use it for the current frame is not very robust and causes little overshoot in some frames on the left and right lanes 

Another shortcoming could be ...


#### 3. Possible improvements to my pipeline

A possible improvement would be to use some other mechanism instead of averaging the lines to get a  better fit on the lanes.

Another possible improvement would be to experiment with dilation and erosion to get better match on the lines immediately after Canny edge detection is done.