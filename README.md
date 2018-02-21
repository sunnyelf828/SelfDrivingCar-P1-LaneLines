# Self Driving Car P1 Finding Lane Lines on the road
This is the Project 1 of Udacity CarND. The project is to develop an automatic lane lines finding program. 

------

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:

- Make a pipeline that finds lane lines on the road
- Reflect the work in a written report

##Summary:

In this project, I'm using color selection, region of interest selection, grayscaling, Gaussian smoothing, Canny Edge Detection and Hough Tranform line detection techniques to piece together a pipeline to detect the line segments in the image, then average/extrapolate them and draw them onto the image for display After that, apply the pipeline to a realitic video stream as an automatic lane line finding programs.

The pipeline consisted of 5 steps. First, I converted the images to grayscale, then I filtered out some noise through a Gaussian Blur, then using Canny algorithm ot selected edges with large gradients. For most of the time, the driver would only focused in the region in front of him/her. I selected edges within this region and mask the other part in the images to be black(ignored).Through Hough Transform, I extracted the related lines (sometimes to be fragmented lines since in the real world both dashed lines and solid lines are existed on the road) from the image and plot the extracted lines overlaying on the original image.



## Reflection

##### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I filtered out some noise through a Gaussian Blur, then using Canny algorithm ot selected edges with large gradients. For most of the time, the driver would only focused in the region in front of him/her. I selected edges within this region and mask the other part in the images to be black(ignored). Through Hough Transform, I extracted the related lines (sometimes to be fragmented lines since in the real world both dashed lines and solid lines are existed on the road) from the image and plot the extracted lines overlaying on the original image.



    image = mpimg.imread("test_images/"+item)
    f,ax = plt.subplots(1,2,figsize=(12,12))
    gray = grayscale(image)
    blur_gray = gaussian_blur(gray, kernel_size)
    edges = canny(image, low_threshold, high_threshold)
    
    masked_edges = region_of_interest(edges, vertices)
    
    line_img = hough_lines(masked_edges, rho, theta, threshold, min_line_length, max_line_gap)
    color_edges = np.dstack((masked_edges, masked_edges, masked_edges)) 
    line_edges = weighted_img(line_img,image,α=0.8, β=1., λ=0.)
    
    ax[0].imshow(image)
    BoxCoords = vertices[0,]
    boxPolygon = Polygon(BoxCoords, edgecolor='b',ls='dashed',lw=3,fill=False,closed=True)
    ax[0].add_patch(boxPolygon)
    ax[1].imshow(line_edges)
`cv2.inRange()` for color selection
`cv2.fillPoly()` for regions selection
`cv2.line()` to draw lines on an image given endpoints
`cv2.addWeighted()` to coadd / overlay two images `cv2.cvtColor()` to grayscale or change color `cv2.imwrite()` to output images to file
`cv2.bitwise_and()` to apply a mask to an image

In order to draw a single line (other than the fragmented lines) on the left and right lanes, I modified the draw_lines() function by extract the slope of all the related fragmented lines and average them so as to get a smooth single line on both left and right lanes.



##### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming would be what would happen when the lane lines are having a large curvature, the smoothed lane lines may crossover which is not the the real case.

##### 3. Suggest possible improvements to your pipeline

A possible improvement would be monitoring the adjacent slopes to be within some coherence other than directly smooth over all the slopes.



