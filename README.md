[//]: # (Image References)

[solidWhiteCurve]: ./test_images_output/solidWhiteCurve.jpg "Solid White Curve"

[solidWhiteRight]: ./test_images_output/solidWhiteRight.jpg "Solid White Right"

[solidYellowCurve]: ./test_images_output/solidYellowCurve.jpg "Solid Yellow Curve"

[solidYellowCurve2]: ./test_images_output/solidYellowCurve2.jpg "Solid Yellow Curve - 2"

[solidYellowLeft]: ./test_images_output/solidYellowLeft.jpg "Solid Yellow Left"

[whiteCarLaneSwitch]: ./test_images_output/whiteCarLaneSwitch.jpg "White Car Lane Switch"
# **Udacity - Self-Driving Car NanoDegree**
# **P1 - Finding Lane Lines on the Road** 
![whiteCarLaneSwitch][whiteCarLaneSwitch]
---

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on the work done in this report

---

### Reflection

### 1. Pipeline description.

#### The pipeline consists of 5 steps:
1. Converted the input image to grayscale.
2. Found Canny edges on a blurred version of grayscale image from step - 1.
3. Masked out the regions that would not contain the lane lines; this is approximately triangle having   
its base across the image's bottom width and the top of the triangle at around 50% height.
4. Draw Hough lines within the masked region of the image from step - 3. I had to tweak various parameters    
of Hough lines function to have it return consistent results for videos, including the challenge.    
The draw_lines() function also required some work which I will discuss shortly.
5. The image received from step - 4 consists of left and right lanes drawn on a black background.   
We use this image as an overlay (using weighted_image() function) on the actual image.   
This gives us an image annotated with coloured lines dawn over the lanes.

#### The draw_lines() function
Given a set of point pairs, each representing a line, I classified them as left or right lane   
by examining their slope. if the angle (arctan of slope) is less than 0 it is a left lane   
otherwise it is a right lane.   
This procedure worked well but failed in case of the challenge video. The reason being there were    
a lot more edges (Hough lines) in the region of interest which were near horizontal slope and were being   
used as input to line fitting algorithm. This led to the line being unstable as and when there were such   
in the frame. 
To reduce this instability, I filtered out all the lines that had an angle of either over 25 degrees or    
less than 75 degrees. This gave me a list of lines that would mostly resemble the slope of the lanes.   
i.e. not really close to being horizontal or vertical.   
Once I had a list of lines classified left and right, I used the fitLine() function to get two lines.
Using these lines, I calculated the points on the line that would lie at the border of region of interest,      
drew the lines joining the two extreme points on each line and returned the frame overlaid with the lines to be appended to the video.

#### Sample Image Results
![solidWhiteCurve][solidWhiteCurve]
![solidWhiteRight][solidWhiteRight]
![solidYellowCurve][solidYellowCurve]
![solidYellowCurve2][solidYellowCurve2]
![solidYellowLeft][solidYellowLeft]
![whiteCarLaneSwitch][whiteCarLaneSwitch]

### 2. Potential shortcomings with the current pipeline

1. The region of interest has been hard-coded to cater the provided videos; this is not robust enough to work consistently     
with many scenarios. e.g. a steep curve would certainly fail to have a reasonable overlap with the region of interest.
2. The lane lines in the pipeline are drawn from what information is present in the current frame. This makes is unstable     
and has a jitter. Sensitive to edges in the current frame that are close to the slope of interest.


### 3. Possible improvements to the pipeline

1. Historical data would probably help to a degree in cases of steep changes to the current frame information.     
e.g. In case of a steep curve, the lanes from the previous frame would already have started to lean towards the curve,    
this information can be used to adjust the region of interest.
2. Again, historical information would help average out the sudden changes brought in by stray edges. Smoothing out the changes to lane equation would help in making the lanes steady.
