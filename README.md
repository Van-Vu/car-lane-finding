# **Finding Lane Lines on the Road** 


The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./output_images/HLS_colorspace.jpg "Grayscale"
[image2]: ./output_images/RGB_colorspace.jpg "Grayscale"
[image3]: ./output_images/HSV_colorspace.jpg "Grayscale"
[image4]: ./output_images/YUV_colorspace.jpg "Grayscale"
[image5]: ./output_images/HLS_edge.jpg "Grayscale"
[image6]: ./output_images/RGB_edge.jpg "Grayscale"
[image7]: ./output_images/HLS_roi.jpg "Grayscale"
[image8]: ./output_images/Final_line.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

Since the lane lines consist of one white line and one yellow line. My first step is to detect those 2 lines at the same time

**1-** I converted the image into different color space to find the best result:

![alt text][image1]

![alt text][image2]

![alt text][image3]

![alt text][image4]

I am happy with RGB and HLS

**2-** I converted the image to Grayscale then 

- gaussian_blur: kernel size = 3

- canny transform: low_threshold = 10, high_threshold = 150

![alt text][image5]

![alt text][image6]

I am happy with HLS

**3-** I used `region_of_interest` function with the bounding box:
```python
def get_vertices(image):
    imshape = image.shape
    return np.array([[(120,imshape[0]),(420, 330), (imshape[1] - 420, 330), (imshape[1]-50,imshape[0])]], dtype=np.int32)
```

![alt text][image7]

**4-** I called the `cv2.HoughLinesP` to detect the lines

**5-** I modified the function `draw_lines`:
- Calculate the slope of the line to determine if it belongs to the right / left line: `slope = (y2-y1)/(x2-x1)`
- I used `left_z = np.polyfit(left_x, left_y, 1)` and `left_f = np.poly1d(left_z)` to fit all detected point in one line
- `cv2.line(image, (left_f(imshape[0]).astype(int), imshape[0]), (left_f(half_y).astype(int), half_y), color, thickness)`: this line draw a left line from the bottom left to the half-image point using the polyfit found above

![alt text][image8]

**6-** The function `process_image` is the final pipeline to find the lane lines

Here is the link to result [solidWhiteRight](./test_videos_output/solidWhiteRight.mp4), [solidYellowLeft](./test_videos_output/solidYellowLeft.mp4) and the [challenge](./test_videos_output/challenge.mp4)

### 2. Identify potential shortcomings with your current pipeline

- The solidWhiteRight video looks fine but solidYellowLeft at 11-12 second the lines flash with wrong detection, this is a sign that the HoughLinesP parameters need to be tuned up

- If the middle point `half_y` is increased, the line will be in wrong position

- Can't detect curve lane-line since there's no way for me to draw a curve line at this point


### 3. Suggest possible improvements to your pipeline

- Extract the images in the period of 11-12 second in solidYellowLeft and fine-tune the HoughLinesP parameters

- Region of Interest is very tightly choosen based on several attempts, there must be a way to detect that ROI box programmatically
