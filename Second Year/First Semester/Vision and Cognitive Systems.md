
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
#### Edges
There are multiple version of edges, regarding those created by a change of color, we can easily see that they can be identified by looking at the derivatives of pixel colors.

![[Pasted image 20241030144035.png]]

To perform edge detection we can just apply a convolution of the image with a filter that simbolizes a derivative operation. There are many such filters but the most common one is the Sobel filter. The Sobel filter has 1D derivatives along rows and performs a weighted average.

![[Pasted image 20241030144404.png]]

Many other such filters exist, with different weighting in the other direction.
So the idea is to first calculate a gradient version of the image and then isolate edges by checking if the gradients are above a certain threshold. To work with bigger dividing lines we need to check if a certain pixel is the local maximum along the gradient direction, then select the single maximum along the width of the edge.

Another problem that can arise is the fact that, depending on the magnitude of the threshold, certain softer boundaries might be disregarded. On the other hand, if the threshold is too low we introduce unwanted boundaries. The solution to this is starting an edge with a high threshold and continuing it with a low one. 