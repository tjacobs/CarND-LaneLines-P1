#**Finding Lane Lines on the Road** 

---
### Reflection

###1. The Pipeline

My pipeline consists of five steps. 

First we Gaussian blur the image to smooth jaggies.

We don't greyscale the image, because in the yellow lane video, the yellow lane line comes out to be very similar brightness to the grey road colour when turned to greyscale.

Second we perform Canny edge detection to get an image that just has edges in it shown.

Then we mask off a region that we expect to see the lane lines in.

Then we detect lines with a Hough transform.

Now we have a list of detected line co-ordinates.

We bucket the lines into left lines and right lines based on slope.

We can draw these over the video for each frame, which works fine, but often but it’s a bit of a mess.

![A bit of a mess][https://cdn-images-1.medium.com/max/800/1*2tIiir1f5WXHBCCZLhPsBQ.png]


###2. Identify potential shortcomings with your pipeline




###3. Suggest possible improvements to your pipeline

