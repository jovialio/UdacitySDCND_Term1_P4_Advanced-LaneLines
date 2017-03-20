# Advanced Lane Finding
**Please refer to P4.ipynb for results and explanation for a better understanding of the flow in this project. Below is a summary of the steps taken in the project.**

## Step 1 - Image calibration
Obtain corners of calibration chessboard image and display image.

## Step 2 - Undistort chessboard image (Criteria 1)
Undistort chess board image to verify result of undistorting the image

## Step 3 - Undistort on test images (Criteria 2)
Perform undistortion on test images to verify results.

## Step 4 - Import codes from Project 1 Lane Drawing
Codes from project 1 lane drawing are imported to make use of the line drawing function for perspective transformation. Lane lines drawn will be transformed into vertical lines and verified.

## Step 5 - Warp test image (Criteria 4)
Test images are warped for a top down view and verified for warping and unwarping of image

## Step 6 - Define functions for color and gradient thresholding (Criteria 3)
The following functions are defined for the following purpose
1. Sobel thresholding - Perform sobel thresholding operation (between 20 and 100) on both X and Y direction
2. Magnitiude of gradient thresholding - Perform overall magnitude thresholding (between 20 and 100) of the gradient in both x and y
3. Direction thresholding - Perform direction thresholding for near vertical lane markings (between 0.0 and 0.7)

Additional color thresholding is performed by using
1. R channel of RGB with thresholding between 220 and 255 to extract white lines. Value is chosen to balance noise vs detection.
2. S channel of HLS with thresholding between 120 and 200 to extract yellow lines.
3. Convert to HSV color space and perform addtion extraction of yellow (between [0,80,200] and [45,255,255]) and extraction of white (between [0,0,225] and [255,30,255]).

**Perform OR operation on all individually extracted features to create final image for lane detection.**

## Step 7 - Define function used for sliding window detection
1. Functions defined for sliding window search using peak of histogram as starting point of search. Image is split down the center and location x of peak value is found for each half of the image.
2. Using lane lines found via sliding window, fit polynomial line and plot line

## Step 8 - Display sliding window detection
Display sliding window detection results for verification.

## Step 9 - Plot lines and draw on original image using results from sliding window (Criteria 5, 6 and 7)
Plot lines using result from sliding window and verify left and right line for parallel, line separation and similar radius. Once left and right lines are verified to be good, unwarp image to recreate original image with lines drawn.

## Step 10 - Define Line class and pipeline function (Criteria 9 and 10)

### Line Class
To track recent measurements, the following main variables and functions were written.

#### Variables

1. n: To define the number of frames to be used for averaging
2. miss_frame_counter: Count number of frames missed where lines drawn did not meet criteria of parallel, distance separation or radius
3. detected : To track if frame detected lines
4. recent_radius: To track last n valid radius detected 
5. radius_of_curvature: Average radius of last n radius
6. recent_best_fit: Polynomial coefficients for the last n iterations
7. best_fit: Polynomial coefficients averaged over the last n iterations        
8. bestx: Using best_fit, plot all x values for a range of y
9. current_fit: Polynomial coefficients for the most recent valid fit         
10. recent_center: To track last n valid center values
11. line_base_pos: Average center values of last n valid frames

#### Fuctions
1. average_polynomial: Average polynomial values from recent_best_fit and assign to best_fit
2. plot_fit: Plot bestx using best_fit polynomial
3. cal_radius: Calculate average radius using last set of recent_radius
4. cal_center: Calculate average distance from center using recent_center

### pipeline function
To generate the video, pipeline video follows the following flow.

1. Process image to undistort, warp and generate binary image of lane lines.
2. If missed frame counter is < n and it is not the first frame of video, use last found lane line and search +-10 (x direction) of line. Else, perform histogram and sliding window search.
3. Calculate radius and line separation, and check that lines are similar in radius, parallel and of appropriate separation. 4. If all 3 conditions are met, consider the current frame a valid frame and proceed to add current radius, distance from center and fit coefficients to line class. Calculate the average values of distance from center, radius and fit coefficient from a maximum of n frames and use the values to display and plot lane lines.

## Step 11 - Test Line Class and pipeline on test images.

## Step 12 - Test on project video (Criteria 8)

# Improvements to algorithm

Current algorithm was able to handle the project video well but it was unable to handle the challenge and harder challenge video. A line was drawn on the left of the left lane line for the challenge video due to the cast shadow from the center divider that was picked up by current algorithm. 

Improvements can be made to current algorithm by looking into means of shadow removal or other color spaces such as LAB and LUV instead of HSV as there were discussion on Slack that mentioned S of HSV picking up much more noise from shadows. B channel of LAB might work well to pick up yellow lanes, and L channel from LUV might works well to pick up white lanes, and both without the shadow noise that comes with S channel of HSV. 
