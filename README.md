# Project 3: Face Morphing! :) 

*Follow along with the code here: https://github.com/CamilleDang/proj3

#  Overview

During this project, I morphed faces together by selecting correspondence points of important facial features, computing the Delauney triangulation of the points, and corresponding affine transformations, in order to successfuly warp the face structure and used cross-dissolving to interpolate corresponding colors! This allowed me to morph between images (ex: images of my mother and me, and me at different ages!) as well as calculate the 'average face' of two images or of a population.

#  Part 1. Defining Correspondences

I chose to morph between my mother and me! 
In order to do this, I first selected two images of us in similar positions and cropped them to be the same size. I then selected 60 correspondence points at key feature points (facial features such as eyes, nose, mouth, rim of face, rim of hair + 4 points at each corner of the image), using matplotlib and ginput, and stored each mouse click in an array of points, with a button press indicating the end.
I then computed the Delauney triangulation of the corresponding points (using `scipy.spatial.Delaunay`), which enabled triangle-to-triangle correspondences for both images.

| Mother Triangulation | Me Triangulation | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="ma_delaunay.png"> |  <img width="400" src="cam_delaunay.png"> |

#  Part 2. Computing the 'Midway' Face

The goal was now to compute the 'midway' face between my mother and me. In order to do this, I completed the following steps:
1. Compute the average points by adding mine and my mother's correspondence points and then dividing in half.
2. Compute the Delauney triangulation of the average correspondence points.
The goal was then for each image, warp each image to the average shape, also sending the correct color pixel from the original image to the corresponding location in the new average shape.
3. Create a function to calculate the affine transformation matrix between a set of 3 original/'source' (x, y) points and 3 destination (x, y) points.
Complete for both images:
4. For each triangle in the midway triangles:
   
   a. Find corresponding vertex coordinates for triangle in midway image.
  
   b. Find corresponding vertex coordinates for triangle in original image.
  
   c. Compute the affine transformation matrix between the vertex coordinates of original triangle and the vertex coordinates of midway triangle.
  
   d. Compute the inverse of the affine transformation matrix, which will be used for inverse warping: using the vertex points from the midway triangle to find the corresponding vertex  points from the original triangle.
  
   e. Grab all the points within the midway triangle, using `skimage.draw.polygon`.
  
   f. Find the original points by multiplying the points found in part e with the inverse matrix found in part d (to ensure dimensions were right, I padded the points found in part e   with a column of ones)
  
   g. Using scipy.interpolate.griddata and the 'nearest' method, interpolate the color from the original triangle to the corresponding location in the midway triangle.
  
6. This should yield both images warped to the average shape!

| Mother Warped to Average | Me Warped to Average | 
|:-------------------------:|:-------------------------:|
|<img width="400" src="output_image.png"> |  <img width="400" src="cam_interpolate.png"> |

6. Average the two images to create the 'midway' face by cross-dissolving the two images like such: `(1 - frac) * im1 + frac * im2`, with frac being 0.5

| Mother Original | Me Original | Mother & Me 'Midway' |
|:-------------------------:|:-------------------------:|:-------------------------:|
|<img width="400" src="macrop2.png">  |  <img width="400" src="camcrop.jpg"> | <img width="400" src="morphed_test.png"> |

The results turned out really well! I covered most of this logic (except for cross-dissolving) in the function `warp_from_avg(im1, im1_pts, avg_pts)` to reuse for warping one image to an average of that and another image.

# Part 3. The Morph Sequence

Using the logic from above, I created the function morph, which morphs two images based on 6 inputs: the two images and their corresponding points as well as the warp_frac and dissolve_frac, which respectively represents the weight of the shape configuration and the weight of the cross-dissolving.

      def morph(im1, im2, im1_pts, im2_pts, warp_frac, dissolve_frac):
          avg_shape = (1 - warp_frac) * im1_pts + (warp_frac * im2_pts)
          endres1 = warp_from_avg(im1, im1_pts, avg_shape)
          endres2 = warp_from_avg(im2, im2_pts, avg_shape)
          res = cross_dissolve(endres1, endres2, dissolve_frac)
          return res

Using this function, I created a gif that shows the incremental morphs from my mom's face to mine! I made the gif with 50 frames at 50 milliseconds each.

<img width="500" src="morphed_w_interp.gif"> 

# Part 4. The "Mean Face" of a Population



# Part 5. 

