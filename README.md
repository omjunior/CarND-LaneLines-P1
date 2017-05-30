# **Finding Lane Lines on the Road** 

## Reflection

### 1. Describe your pipeline.

The goal of this pipeline is to identify the two bordering lane lines adjacent to the car, and mark them with a red overlay.

The steps are as follow:

![alt text][image1]


#### 1.1 Noise filtering

The filters applied are a bilateral filter followed by a gaussian blur.

With the former it is possible to use a more agressive filtering, preserving the edges of the image.
The latter is then applied to remove any sharp thin lines (as some marks on the asphalt in the challenge video).

![alt text][image2]

#### 1.2 Canny edge detection

The Canny algorithm is now executed in order to find the edges of the image.

Notice that in this point the image is still in color. When converting an image to grayscale, any constrast of color that have
the rough same ammount of luminosity is confounded, rendering the Canny edge detection unable to find them.

In this approach the algorithm is applied to each channel separately, and the results are merged by using the max funcion element wise.
This ensures that a change in color will be represented in the Canny output.

![alt text][image3]

#### 1.3 Region selection

A mask is aplied over the image, removing any edges detected outside the region of interest, which consist of a trapezoid shape encompassing
the lane lines.

![alt text][image4]

#### 1.4 Hough lines

The Hough lines detection algorithm is applied, searching for rectilinear segments in the edges image.

For every line found three measures are taken: the slope of the line (*m*), the offset (*b*) and the length (*l*).

Any line in which either the slope is less than 30º or vertical is discarded. The remaining lines are sorted according to the slope sign
and classified as being part of the rigt or left lane line.

![alt text][image5]

#### 1.5 Averaging of the lines

For each group of lines, averge values of *m* and *b* are computed, weighted by the length of the line segment.

##### 1.5.1 Image

If the object being processed is a still image, a line is then drawn spanning from top to bottom of the region of interest using the computed *m* and *b*.

##### 1.5.2 Video

If the image is a frame from a video, the values of the *n* last frames are also taken in to account, in order to have a smooth movement of the lines.
For this work 12 frames (about half a second) were used.

The *m* and *b* values are averaged, using larger weights the more recent the frame is. As with the image case, lines are extrapolated from top to bottom
of the region of interest.

![alt text][image6]

### 2. Identify potential shortcomings with your current pipeline

This aproach with a fixed region of interest is not versatile. Any change in the position of the camera will not be tolerated by the algorithm.

It also depends on a good contrast of the lines. Although I did not favor any particular tone (no color masking), faded lines will probably not be
encountered.

And of course, it will only work when the car is centered on a particular lane, with unobstructed view of the lines. When changing lanes, or if another
car is over a line the algorithm will have a hard time finding these lines.



### 3. Suggest possible improvements to your pipeline

If one wants to restrict certain tones of lines (only white or yellow) color masking can be helpful to find the lines. But as many places have different
conventions, I decided not to go with this approach.

To overcome the fixed region of interest problem, it could be possible to try to find some 'significant' lines, choose them by some kind of voting,
then remove outliers until two main lines are found.
It could be useful to use the latest frames also, since things do not change much from frame to frame.

Also, as I do not have much knowledge of image processing, there might be a color representation scheme such as YUV or YCbCr that might perform better
with the edges detection algorithm.

Finally this algorithm is very dependent on the several parameters that were 'cooked' for these examples.



[image1]: ./test_images/solidWhiteCurve.jpg "Original"
[image2]: ./test_images_output/solidWhiteCurve_filter.jpg "Bilateral and gaussian filtering"
[image3]: ./test_images_output/solidWhiteCurve_canny.jpg "Canny edge detection"
[image4]: ./test_images_output/solidWhiteCurve_roi.jpg "Region of interest"
[image5]: ./test_images_output/solidWhiteCurve_lines.jpg "Hough lines"
[image6]: ./test_images_output/solidWhiteCurve.jpg "Final"

