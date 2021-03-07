# **Finding Lane Lines on the Road**

### Author Giorgio Corbellini


---

Full description of the pipeline is available in the wrietup document "P1_lane_detection_writeup.pdf"
Finding Lane Lines on the Road


1. Intro 
My pipeline consisted of 7 steps.

1. I use the inRange function to apply yellow and white masks to the input image

2. I converted the masked image to grayscale

3. I applied gaussian blur

4. I detected the image edges with the Canny detector

5. I defined the region of interest (RoI) for our usecase (a trapezius)

6. I detected lines within RoI (using the Hough transform) & drew detected lines

7. I overlay the detected lanes with the original image


The resulting pipeline works ok for both test videos “solidWhiteRight.mp4” and “solidYellowLeft.mp4”. The pipeline also works for the challenging video but the performance is not so good because of the presence of shadows and different pavement colors. All videos are available in the folder “test_videos_output” 

2. Identify potential shortcomings with your current pipeline

I decided to enumerate the shortcomings so that I can refer to them in the improvement section.

Shortcoming 1-parameters: parameters tuning is probably the activity where I spent most of the time. In fact the choice of the parameters of each step of the pipeline has a cascade affect on the following steps. I think that the parameters that I used are ok, but there is room for improvements. Even if I had found better parameters, the pipeline I suggested is not adaptive which means that new training images would require new manual parameter settings.

Shortcoming 2-shape: my solution assumes straight lines ahead and does not adapt the shape of the lane detection in case of sharp curves. Smooth curves are not an issue but the sharp curves are. Which is also shown in the challenge video.

Shortcoming 3-shadows and pavement color: sudden color changes on the the driving surface show the limitation of the color masks I applied at the beginning of the pipeline. Such shortcoming is visible in the challenging video where despite the smoothing, the detected lines are quite unstable. 

Shortcoming 4-dashed/solid lanes: my pipeline does not really treat dashed lines and solid lines in a differ way.

Shortcoming 5-dirt: this algorithm is fully dependent on one single camera input, if the lens is obscured (e.g. direct light, dirt, fog, maybe strong rain etc.) the car might have hard time to detect the lane lines.

3. Suggest possible improvements to your pipeline
Possible to improvements

Shortcoming 1-parameters: parameter selection requires more experience and exploration. There are many resources online to understand the parameter space and the way they effect each step of the pipeline. Parameters are also depending on the lighting conditions of the original image, so more complex calibration rules and functions should be applied for example by detecting the brightness of the scene. Over night, for example, the parameter choice would be completely different.

Shortcoming 2-shape: I should change the way define the edges of the ROI. In a curve the shape is different. In the real world if I had access to a reliable positioning source (GNSS, RTK or cellular) and access to HD maps, I would know where I am on the road and predict the shape curves ahead.

Shortcoming3-shadows and color: the way I apply color masking is not robust enough. In fact even averaging the last 10 lines results in unstable line detections. A possible improvement of the pipeline is use better color filters to reduce the dependency on brightness changes and shadows.

Shortcoming 4-dashed/solid lanes: I observed that it should not be difficult to build two different extrapolators one for dashed lines and one for solid lines looking at their length.

Shortcoming 5-dirt: this is probably very hard to solve with software solution if not impossible to solve. I saw some solutions from Waymo to physically swipe the sensor surfaces every now and then.




