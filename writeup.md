# **Finding Lane Lines on the Road** 

## Writeup 
---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1.  The Total Pipeline
#### Normal Part
The pipeline of the normal lane detection program does not differ much from
the one in the course lectures. The only changed thing is the order of choosing
ROI(region of interest) and Hough Transform. Finding ROI is put before Hough
Transform, so that the lines returned by Hough Transform can be used to vote
the left and right lane. In all, the pipeline consists of:

* Change the Picture into Grayscale

![alt text][image1]

* Use Canny detection to extract edge information by computing the largest gradient

* Compute Region of Interest

* Apply Hough Transforms to return line candidates

* Since we have a strong prior that  there is always a left and right lane, so I simply
group the lines returned by Hough transform into two groups. The grouping is based on 
the intersection coordinates between bottom boundary and candidate lines. 
If the x coordinate of the intersection is smaller than half width, then the line is grouped to left lane.
After the grouping, the mean slope and bias on the x-axis is computed in each group, which
serves as the parameters of the left and right lane. 
During the voting, lines with large slope (absolute value > 2) are discarded


#### Challenge Part
The Challenge video is interesting, I find out there are several challenges in this sample:

* The video size is changed, and the bottom of the video is occluded because of the car.
Thus the ROI should be changed accordingly.

* The detection quality of some frames are bad, making the lane detection vibrates heavily.
I believe in autonomous driving, we always wish the lane detection to be smooth and stable.

* A part of the video suffers from overexposure, which makes the canny transform unstable.

To address these problems, the listed things have been changed:

* The ROI is changed to [(160,700),(640, 430), (720, 430), (1000,700)]

* The prediction of the current frame considers the history prediction, by using a momentum
update: 0.8 * history_line + 0.2 * current_frame_line. When the line is missing from the 
current frame, the history is used directly.

* I have tried gamma correction, but it does not work well. I will go after for solutions in
the near future.


### 2. Potential shortcomings


* The current lane detection method heavily relies on the canny transform, 
which is often affected by brightness, bad weather and etc.

* In this project, mean averaging is used to fuse different lines detected by Hough transform,
a lane with large curvature will heavily destroy the voting schedule. Thus a piecewise-linear
function or polynomial curve may be used in the future projects.

* The region of ROI should be mannualy changed for different situations, which is unaffordable
in the real word applications. 


### 3. Possible improvements

* A stronger image preprocess might be applied to normalize brightness and contrast. In this way,
the stability of Canny transform can be improved.

* Maybe a deep learning based methods like SCNN can perform better when there are lots of training
data.
