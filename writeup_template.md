# **Finding Lane Lines on the Road** 

## Writeup - Juan David Galvis

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road


[//]: # (Image References)
[image1]: ./output_images/01_initial_image.png "Input Image"
[image2]: ./output_images/02_contrasted_image.png "Contrasted Image"
[image3]: ./output_images/03_gray_image.png "Grayscale Image"
[image4]: ./output_images/04_blur_image.png "Blur Image"
[image5]: ./output_images/05_edges_image.png "Edges Image"
[image6]: ./output_images/06_masked_image.png "Masked Edges Image"
[image7]: ./output_images/07_result_image.png "Masked Edges Image"
---

### Reflection

### 1. The Pipeline

Starting with the inoput image: 

![alt text][image1]

My pipeline consisted of 5 steps. 

1. First I improve the contrast of the image by converting it to the LAB Color model, which is widely used in photoshop for color and lightness contrast correction, and CLAHE (Contrast Limited Adaptive Histogram Equalization) on 'L' channel. This allows to bring up lane lines in circumstances where there is low contrast between lane and pavement. This has the downside of sharpening edges of other features like shades and road marks, however with a propper setting of the canny edge detection this can be to certain point avoided.

![alt text][image2]

2. Then I converted the images to grayscale:

![alt text][image3]

3. Then I apply blur filter to eliminate some possible noise and soften some of the edges that became stronger on the first step:

![alt text][image4]

4. After I apply canny edge detection, looking only for strong edges (low_threshold = 80, high_threshold = 240) since in the first step all edges were highlighted.

![alt text][image5]

5. Then I apply two masks (one external and one internal) to define a region where lines should be. It is important to take into account that mask's vertices should be dependat of image size and not fixed values:

![alt text][image6]

6. Finally I apply Hough lines to detect all lines on the masked edges image, and these get processed in the draw_lines function, where I diffirentiate left and right lane lines by their slope (slope > 0 corresponds to right lane line and slope < 0 corresponds to left lane line) Then, slopes get filtered to eliminate horizontal lines, such that if abs(slome) < 0.5, the line isn't taken into account (from the 3 videos I could see that the slopes of the lines are always > 0.6). This way, 2 lines are drawn per image, corresponding to the right and left lane line.

![alt text][image7]



### 2. Potential shortcommings

There are two main shortcomings with the developed pipeline:

  1. Line Correctness: Even though the pipeline works really good in the first two videos and acceptably in the third one (challenge), shades on the road or tire marks affect the pipeline's performance. Maybe some further image processing could help eliminate the effect of shades and marks on the road.
  
  2. Line detection robustness: In some really few cases, there can be no lane detection at all (hough lines doesn't return any line or lines are rejected because of their slope) It happens only for one lane line among all the 3 videos, but it could happen in another videos if the lighting or the conditions change. Adjusting the canny edges or hough lines parameters could help fix this but would also compromise the performance of the pipeline. I think that more robust algorithms should be implemented to avoid this. One easy solution for this particular case would be to save the previous line for when no line is detected.


### 3. Suggest possible improvements to your pipeline

Like said the pipeline works in general well on the 3 videos but I don't think it is robust enough for all the cases, so it definetly needs more robustness for cases with shades, lane marks or other features in the image. Further image processing or other algorithms could be explored.

Sometimes no line is detected (even though it only happened once among all 3 videos), it is not guranteed that it won't happen more if we try with another videos, for this, the solution could be to save some of the previous detected lines and applying a low pass filter to the parameters of the line (m and b) since lane lines slope and location shouldn't change that fast.
