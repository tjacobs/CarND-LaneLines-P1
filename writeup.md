#**Finding Lane Lines on the Road** 

---
### Reflection

###1. The Pipeline

My pipeline consists of nine steps. 

1. **Blur.** First, we Gaussian blur the input image using a 7x7 kernel. This helps smooth and remove spurious gradients that we don't want to detect as real world lines.

2. **Canny.** Second, we perform Canny edge detection to get an image that just has edges shown, using threshold values low 50 and high 150. Note: We don't greyscale the image, because in the yellow lane video, the yellow lane line comes out to be very similar intesnity to the grey road colour around it when transformed to greyscale.

3. **Mask.** Then, we mask off a region that we expect to see the lane lines in. We define a road shaped trapeziod, using the full image width on the bottom two points, and positions 45% and 55% the way across the image horizontally, at 60% the height of the image. This gives us a region that looks like a road receding into the distance.

4. **Hough.** Then we detect lines in that masked region using a Hough transform. After some experimentation with parameters over multiple images and videos, I end up using parameters rho 1, theta pi/180, threshold 20, minimum line length 10, and maximum line gap 90.

5. **Bucket.** Now comes the fun part. We have a big pile of lines, and we want to just choose just two: the left lane and the right lane line. So we go through all the lines and bucket them into two buckets: left lines and right lines, based on their calculated slope. We also filter out lines with outragious slopes, so that only lines with slopes between 0.5 to 1.5 (and negatives for the other line) make the cut. This gives us the roughly 45 degree lines either way.

6. **Median.** An earlier implementation of the pipeline then took the mean of the line slopes, but I found that didn't perform as well as the median. So next we sort the slopes of the left lines, choose middle slope, along with its starting x and y co-ordinates. We project a line from that, and do the same with the right lines.

7. **Intersect.** We want to extend the left and right lines all the way down to the bottom of the image, and roughly to the horizon line zero point, so we first define top and bottom horizontal lines over the image. Then we find take the chosen median left and right lines and calculate where they intersect with these top and bottom lines. These co-ordinates form the two lines that are our best estimate of where the left and right lane lines are in this frame.

8. **Smooth.** There are some frames in the video that fail to detect lines, and some frames that have mis-detected lines. To reduce jitteriness from frame to frame of the stream, we use information from previous frames to better estimate where the lines are. In the frame that we first detect lines, we start our smoothed lines off with those co-ordinates. Then for each following frame, we add only 10% of the newly detected line's difference from the previous frame's lines into the co-ordinates to our lines. This gives us much less jittery lines over the course of the video, and helps keep the detected lines on screen for sequences of frames where the lines are temporarily not clearly detectable.

9. **Draw.** Finally, we draw the time-smoothed lines over the top of the original image, using a weighted image merge to render them.

Below are a few image of the process.

Before slope filtering the detected lines, sometimes it can be a bit of a mess.

![A bit of a mess](https://cdn-images-1.medium.com/max/800/1*2tIiir1f5WXHBCCZLhPsBQ.png)

After slope filtering and on easily detectible lines, the detected lines clearly map to the lane lines.

![Fine](https://cdn-images-1.medium.com/max/800/1*Dh0GasBe40aNZfQmhu2fgQ.png)


###2. Identify potential shortcomings with your pipeline

The pipeline has a few potential shortcomings:

1. As you can see in the challenge video, the pipeline does not perform perfectly where there are curves in the road, as it is based on modelling two straight lines only.

2. An earlier version of the pipeline that greyscaled the image had trouble with the yellow line, as the intensity of it was similar to the road, but the colour processing version performs fine.

3. The region of interest mask and the intersecting top line are hardcoded values which must be calibrated with the camera mounting position, and will fail when the road has sharp corners, and especially hills, so that the curvature of the road is in the top section of the image on the ascent of a hill, or not visible at all when the car is at the top of a hill and the road is below the image.

4. Lack of well defined (painted etc) lane lines will make the pipeline fail, along with multiple or false lines on the road.

###3. Suggest possible improvements to your pipeline

Addressing the shorcomings above:

1. Modelling curved lines would allow the pipeline to more accuratly map its lines to those on the road.

2. Adding some form of dynamic brightness or alternative pre-processing modes and confidence factor assessing would allow the pipeline to operate under more varied lighting and paint colour conditions.

3. Adding a form of dynamically pre-detecting the region where the road currently is would allow the pipeline to work in conditions where the road is not in a fixed region of the image, which would be much more robust than hard-coding values.

4. Adding a system to integrate car-ahead detection and relating that to the multiple lines detected on the road would help with selecting the correct two lines out of many that the pipeline should be detecting and tracking. I know when I drive in low-lane-line-visibility conditions, I take a lot of cues from both the cars in front, curbs, parked cars, and oncoming cars.


###4. Results

The pipeline correctly detects the lines in the sample images and the videos, for most of the duration of the video. For the times where it doesn't, it mostly holds its lines in the same position as previous frames, which is still useful for a smooth ride. It still has trouble locking onto small dashed lines as shown in the last added video.