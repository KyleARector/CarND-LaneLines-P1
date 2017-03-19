## Project 1 - Finding Lane Lines
### Project Goals
- Make a pipeline that finds lane lines on the road
- Reflect on your work in a written report

---

### Reflection

#### Image Pipeline

My pipeline consists of several steps. First, the image or video frame is converted to gray scale. Next, a Gaussian blur is applied to the image, smoothing the gradient transition between individual pixels.

After the blur is applied, Canny edge detection is run. This process highlights the biggest gradient changes between pixels, which is indicative of a light area on a dark background, or vice versa.

After edge detection is run, a layer mask is applied to the frame to only allow consideration of a specific area in downstream processes. The mask is outlines by a polygon that is proportional to the size of the image.

After the area of interest is highlighted, a Hough transformation is run. This process generates line segments on the edges found with the Canny algorithm, and only in the area of interest. The end points of these line segments are sent to the "draw_lines" function, which was modified to draw only one line per lane line.

In order to draw a single line on the left and right lanes, I modified the "draw_lines" function to calculate the slope of each line found with the Hough transform. Knowing the slope allows the determination of which lane side the line segment belongs to; Segments with a positive slope are on the right side, and those with a negative slope are on the left. During this process, I included a conditional statement to help filter out primarily horizontal lines (slope with more lateral movement than vertical).

Once the line segments are assigned to a lane side, the average position of the end points is determined, with an upper and lower endpoint for both sides. Using slope intercept form, the intersection with the bottom of the frame is found, and the line extrapolated to the bottom of the frame, as well as the top of the area of interest. Finally, the lines are drawn over a copy of the original frame or image.

#### Shortcomings
The biggest shortcoming would be how my pipeline handles roads with much more curvature over a short distance. The
output of the "draw_lines" function would not represent the lane lines correctly, as it takes the average position of
all the left lane lines. As it runs currently, on any significantly curved roads, the drawn lane line would cut across
the lane on one side, and into the next lane on the other.

Additionally, my pipeline relies heavily on Canny edge detection and Hough transformation with very little preprocessing
in the realm of color. In other situations where the day was not as bright, or if it was raining, the pipeline may not
perform as well.

#### Potential Improvements
There are many potential ways to improve my pipeline. A more ideal solution would use a more dynamically constructed
image mask, possibly generated after an initial pass with Canny edge/Hough transformation on the whole image. The image
could then be masked to only consider areas with higher concentrations of connected pixels. This may help with curving
roads.

Similarly, with a polygonal mask area, curved roads could be handled better. The "draw_lines" function could even be
modified to represent the curvature of the road. Instead of drawing two lines (one per side), it could be used to draw
several smaller samples of a curve.

Additional color filtering at the front may allow better lane line detection.

#### Challenge Video
My pipeline had moderate success in its first run with the option challenge video. Because I made the area of interest proportional to the frame size, the mask was already in the correct place. The biggest issue with the challenge video is the color of the road and the curvature. Because the gradient is not as extreme between the lane line and road surface, my current parameters may not completely detect the lane lines.
