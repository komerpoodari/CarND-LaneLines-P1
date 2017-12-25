# **Finding Lane Lines on the Road** 

## Writeup 
---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Exercise the pipeline on the example images and videos
* Fine-tune the parameters and implement solid lines
* Reflect on the learnings


[//]: # (Image References)

[image1]: ./test_images_output-interim/solidYellowCurve.jpg "without line fitting: solidYellowCurve"

[image2]: ./test_images_output/solidYellowCurve.jpg "with line fitting & extrapolation: solidYellowCurve"

---

### Reflection

### 1. Pipeline Description

As per the project requirements the pipeline stages are used for identifying and plotting on the left (solid yellow or broken white) and right side (solid or broken white) lines.
**1. Convert image to grayscale**
This step is required to simplify the image to extract right features, i.e. in this line segments representing lane boundaries.

**2. Apply gaussian blur**
This step is required to smoothen the image and avoid any noise induced line segment detection. By accidentally I edited kernel size to 4, got a an error during runtime. I understand that kernel size shall be usually odd. I did not experiment with kernel sizes, as 5 is commonly used. The results were fine.

**3. Apply Canny edge detection**
This step is required come-up with edges using Canny method. The output of this would be used to create masked edges with only the region of interest to capture immediate lane boundary infomation. The thresold used are, low threshold = 50, and high threshold = 150, which is adequate range.

**4. Create masked edges by applying region of interest**
This is an important step. I created region of interest _with relative positioining of vertices with respect to image shape/size, instead of usng hard-coded fixel values._ This approach makes the pipeline work even when the image frame sizes change. The output of the stage is the masked edge resultng out of bit-wise and of mask and region of interest determined by vertices.

**5. Apply Hough Transform and draw lines**
In this step, I applied the Hough transform with voting threshold as 30, minimum line length as 10 and maximum gap as 150 upon doing few trials.
Without changing the draw-lines functions the detection was as follows, with just line segments written on the image.
![alt text][image1]

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by doing the following steps.
1. Compute the line segment slope.
2. Exclude line segments that fall into near horizontal lines.
3. Group remaining line segments into right lane and left lane segments.
4. Fit the poli line fit with 1st degree (straight line).
5. Get the slope and intercepts for right and left lanes after fitting
6. Extrapolate the lines to start from the bottom of the image and extend out to the top of the region of interest. 
   I also changed the functions draw_lines() and Hough transform to take vertices as an additional mandatory argument 
   to pass the region of interest information. I also used a thicker annotation.
An example annotated image after the above mentioned changes is as follows.

![alt text][image2]


### 2. Potential shortcomings with current pipeline
There are couple of potential short coming observed during debugging and running the pipeline on challenge.mp4.
1. In rare instannce,the line segments satifying the slope threshold were zero. I handled this with a check in draw_lines to avoid exception.
   Either I have to further experiment on fine-tuning slope threshold.
2. When the road bends are sharp, the left and right lane annotations are overshooting the lanes. This happened in case challenge.mp4. 
   I expect to learn advanced tools to fix this issue, probably by fitting a curved line rather than straight lines.

### 3. Suggestion possible improvements 
1. Curve fittng rather than straight lines.
2. My Jupyter environemnt has been throwing up OS exceptions, while reading mpeg files. 
   Hopefully this can be fixed by spefifying current environment.
  