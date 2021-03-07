# **Finding Lane Lines on the Road**


## Writeup
### Author Giorgio Corbellini


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/solidYellowLeft_no_color_filter.jpg "no color filter"
[image2]: ./test_images_output/solidYellowLeft_color_filter.jpg "color filter"


---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 7 steps. 

1. I use the inRange function to apply yellow and white masks to the input image

Since the lines on the road are either yellow or white, I applied a mask to filter out other colors. I used the inRange function from open cv to do that. But I am not satisfied with the parameters I applied to the masks (I tried several combinations). The result of the pipeline is quite sensitive to these parameters, as an example I share one still image without ([image1]) and with ([image2]) and color filter. Despite room for improvements in the parameter choice, I used the filter in the following because the videos seem to work well enough.
NOTE: if you want to plot the images using my code, please uncomment the line "lines = cv2.cvtColor(lines, cv2.COLOR_RGB2BGR)" in the process_image function.
![alt text][image1]
![alt text][image2]

2. I converted the masked image to grayscale

This is a straightward process as shown in the class.

3. I applied gaussian blur

I used different values for the kernel size. I used the helper tool from one of the recommended articles (https://github.com/maunesh/opencv-gui-helper-tool) and chose my preferred values. I used kernel size 9 at the end.

4. I detected the image edges with the Canny detector

I tried different threshold values, low=90 and high=200 seem good enough (also visually suggested by the tool mentioned above).

5. I defined the region of interest (RoI) for our usecase (a trapezoid)

I decided to define a trapezoid polygon. I defined the 4 vertices as P1, to P4 and used them later in the code. I tried different values as proportion to the image size. I adapted the position of P1 to P4 as a proportion of the image size so that the RoI logic also works with the optional video.

6. I detected lines within RoI (using the Hough transform) & drew detected lines 

I tried a few different parameters especially resolution (distance and angular) but also the minimum number of votes for a line to be detected. The final parameters works ok in my opinion but I have hard time with dashed lines. In fact a much higher min vote value results in more robust solid lane detection but very poor dashed line detection.

7. I overlay the detected lanes with the original image

This is a straightward process as shown in the class.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function mainly looking at the slope of each hough line, as suggested. All lines on the right of the image have positive slope, while the lines on the left have negative slope (that's because of the orientation of y being from the top to the bottom). Each image of the video has its own set of lines. So per image I have a set of right lines and a set of left lines. The size of each set varies frame by frame as well as the deviation between slope values within each set. 

Drawing a line that has the average slope within the set resulted in unstable lines, especially for the dashed road lanes. The dashed lines, in fact, result in short segments that can have a very different slope. I decided to fit a line that would use "good points" (points on an actual driving lane).
The result seems to work ok for solid and but poorer for dashed lines. Of course, there is quite some room for improvements. I also filtered out some outliers from the slope with some hardcoded threshold values (I observed that most of the slopes have around +-0.6 or +-0.8. These hardcoded choices are probably a poor idea.

The resulting pipeline works "ok" in my opinion but it is not as smooth as in the P1_example video. I tried also to check for high slope variations between frames but I could not get very far with that idea so I dropped it. 


### 2. Identify potential shortcomings with your current pipeline

I number the shortcomings so that I can refer to them in the improvement section.

Shortcoming 1-parameters: parameters tuning is probably the activity where I spent most of the time. In fact the choice of the parameters of each step of the pipeline has a cascade affect on the following steps. I think that the parameters that I used are ok, but there is room for improvements. Even if I had found better parameters, the pipeline I suggested is not adaptive which means that new training images would require new manual parameter settings.

Shortcoming 2-shape: my solution assumes straight lines ahead and does not adapt the shape of the lane detection in case of sharp curves. Smooth curves are not an issue but the sharp curves are. Which is also shown in the challenge video.

Shortcoming 3-dashed/solid lanes: my pipeline does not really treat dashed lines and solid lines in a differ way. 

Shortcoming 4-dirt: this algorithm is fully dependent on one single camera input, if the lense is obscured (e.g. direct light, dirt, fog, maybe strong rain etc.) the car would not detect the lane lines. 


### 3. Suggest possible improvements to your pipeline

Possible to improvements 

Shortcoming 1-parameters: parameter selection requires more experience and exploration. There are many resources online to understand the parameter space and the way they effect each step of the pipeline. Parameters are also depending on the lighting conditions of the original image, so more complex calibration rules and functions should be applied for example by detecting the brightness of the scene. Over night, for example, the parameter choice would be completely different.

Shortcoming 2-shape: I should change the way define the edges of the ROI. In a curve the shape is different. In the real world if I had access to a reliable positioning source (GNSS, RTK or cellular) and access to HD maps, I would know where I am and predict the shape curves ahead. 

Shortcoming 3-dashed/solid lanes: I observed that it should not be difficult to build two different extrapolators one for dashed lines and one for solid lines looking at their lenght. The lines in the example video seem to be very solid an stable (also for dashed lines). Probably I could "reuse" a line from a previous image in case the hough lines of does not result in good lines. 

Shortcoming 4-dirt: this is probably very hard to solve with software solution if not impossible to solve. I saw some solutions from waymo to physically swipe the sensor surfaces every now and then.