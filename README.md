# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

In the first project of the udacity self-driving car course we had to find lane markings in pictures and videos of streets. The detection of the lane marking was basically done in three steps:
* Apply a gaussian filter to the image.(getting rid of noisy parts of the image which makes the next steps more reliable)
* Perform canny edge detection. (detects the edges in the image with the help of the grayscale gradient)
* Use hough transformation to find lines from the edges. (Transforms each point to a line in hough space where the intersection of these lines shows the presence of a line in image space)


## Example Image

Edges from canny edge detection             |  Masked edges
:-------------------------:|:-------------------------:
![](https://github.com/CYHSM/carnd/blob/master/CarND-LaneLines-P1/test_images/pipeline/original.jpg?raw=true)  |  ![](https://github.com/CYHSM/carnd/blob/master/CarND-LaneLines-P1/test_images/pipeline/blur.jpg?raw=true)

Extrapolated lines from Hough transformation             |  Result
:-------------------------:|:-------------------------:
![](https://github.com/CYHSM/carnd/blob/master/CarND-LaneLines-P1/test_images/pipeline/canny.jpg?raw=true)  |  ![](https://github.com/CYHSM/carnd/blob/master/CarND-LaneLines-P1/test_images/pipeline/lines.jpg?raw=true)

## Pipeline
In order to draw a single line on the left and right lanes from the extracted edges, I modified the draw_lines() function. The basic idea is to find out all potential line segments and then average all the involved points into 2 single lines on each side.

### 1. seperate lines on the left side and right side according to their slope.


First, I converted the images to grayscale, then Gaussian smoothing and Canny edge detection are applied to extract edges. Later the extracted line segments from Hough Transform are grouped according to their slope. Segments with slope smaller than -0.5 are regarded as part of right line and larger than 0.5 as part of left line.


### 2. using the weighted least square method to calculate the best fitted line for all points on each side

The weighted least square method is applied to calculate the best fitted line for all points on the left or right side, the weight of points is proportional the length of each line segment and only the longest n segments are going to be taken into account.
    *basci least square: y = kx + b = Ap, where A = [[x 1]] and p = [[k], [b]]
    *add weight: Aw = A * np.sqrt(W[:,np.newaxis]), yw = y * np.sqrt(W)
    *k, b = np.linalg.lstsq(Aw, y)[0]


### 3. potential shortcomings
There several shortcomings of the current method:
    *The result is very sensitve to the parameters of Canny Edge Detection and Hough Transform.
    *The region of interest must be defined for this method to work, yet unexpected situations exist when the vehicle is not heading to the direction of road.
    
### 4.possible improvement

The current method works well on first two videos but failed to be applied on the challenge task, the extracted line tend to be unstable. Instead of using the least squre method to extrapolate lines from points, calculating the average slope directly from line segments from the result of Hough Transform may bring improvement.


### Video White
[![Video White](https://github.com/CYHSM/carnd/blob/master/CarND-LaneLines-P1/white.gif?raw=true)]
### Video Yellow
[![Video Yellow](https://github.com/CYHSM/carnd/blob/master/CarND-LaneLines-P1/yellow.gif?raw=true)]