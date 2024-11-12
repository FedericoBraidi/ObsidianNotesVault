
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

By using these Edge detection algorithms we can identify keypoints, also known as interest points.
Generally, Interest points have certain properties:

- Repeatability: Is found despite geometric transformations of the image.
- Salience: It is distinctive.
- Compactness: There are many fewer points than image dimension.
- Locality: Concentrated in a small area.

Now, how do we find two circular regions that are identical across the original image and the zoomed one. The idea comes from Laplacian of Gaussian filters, used in the detection of circular blob features. Using these filters, the max of the characteristic scale is found for the best value of the region size. Knowing this we can look for a maximum of a corner detector in space and simulatneously a maximum of the Laplacian in scale.

Another function that can be used instead of the laplacian, and which is much easier to compute, is the DoG (Difference of Gaussians). This function appoximates well the behaviour of the laplacian. Using this we move to the SIFT method where the corresponding regions are found by looking for maximums in the DoG both in space and scale.

To make what we found rotationally invariant we calculate the gradient of the keypoints, then all the gradients across the neighbourhood and align them in the keypoint direction to compare them with other candidates.

#### Image Classification
For the task of Image Classification we need two things:

- An Image representation.
- A comparison method between the representations.

An example of the second is one of the simplest ML algorithms, Nearest Neighbours. In this case we have a representation of the images and we classify an image as the same class of its nearest neighbour, for some distance metric. This i very sensitive to outliers so we can use k-NN, an extension where we take into account the k closest points instead of just the closest.
As for the Image Representation, the simplest way to obtain one is to resize all the images to the same size and then flatten the pixel values into a 1D array, which is then used as the representation.

This setup is bad, because it is O(n) at test time, which is when we want fast performance.

Another model, that doesn’t require us to remember the whole images, is the Bag of (visual) words representation. We only remember how many of certain visual primitives were present. We basically extract features from all images seen and create a dictionary of local features, then we give a representation to a new image which is an histogram over the dictionary.

First of all the features are extracted, then they are clustered to reduce the size of the vocabulary, with a clustering algorithm such as k-means.

To keep track of which words are more or less important or discriminative we weight each visual word with its Inverse Document Frequency:

$IDF(j)=\log \frac{number \,of\,documents}{number\,of\,documents\,j\,appears\,in}$

