#**Finding Lane Lines on the Road** 

---
### Reflection

###1. The Pipeline

My pipeline consists of 5 steps. 

First we Gaussian blur the image to smooth jaggies. Then we perform Canny edge detection to get an image that just has edges in it shown.

We mask off a region that we expect to see the lane lines in, and we detect lines with a Hough transform.

Now we have line co-ordinates.

We can draw these over the video for each frame, which works fine, but often but it’s a bit of a mess.

![A bit of a mess][https://cdn-images-1.medium.com/max/800/1*2tIiir1f5WXHBCCZLhPsBQ.png]

First, I converted the images to grayscale, then I .... 

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


###2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


###3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...