
What does it mean to see? 

We could interpret it as having a computerized way of what we do when we see. We should then have a sensing device and an interpreting device that allows us to understand what is present in a given image.

Some of the main challenges in seeing are:

- Illumination and Shadows
- Scale and Perspective
- Viewpoint
- Deformation
- Occlusion
- Clutter
- Intra-class variations
- Inter-class similarities

There are two main goals in computer vision:

- Gaining 3D metric information (i.e. reconstructing a 3D image from 2D ones)
- Gaining semantic information (i.e. understanding what objects are in the image)

The second is the focus of this course.

#### Filters
Filters are operators that convert input functions $f(n,m)$ (in this case mappings that represent images) into other functions $g(n,m)$. There are many different filters with various aims:

- Moving average: Replaces every pixel with the moving average of the 9 surrounding pixels. This achieves a smoothing function:
	$g(n,m)=\frac{1}{9}\sum_{k=-1}^{1}\sum_{l=-1}^{1}f(n-k,m-l)$
- Gaussian filter: This filter gives more influence to closer pixels, it is defined by the $\sigma$ parameter that determines the spread of the gaussian and the amount of the smoothing. It also acts as a low pass filter.