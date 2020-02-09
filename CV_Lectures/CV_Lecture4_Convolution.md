## Lecture 4 - Convolution

### **Convolutions**:

* A fundamental and powerful operation in signal/image processing.
* Has been hypothesised as a good model of low-level biological visual processing.
* Convolutional Neural Networks (**CNN**s) - big part of recent wave of deep learning.

#### **Can be used for**:
* Edge detection
* Image denoising
* Edge enhancement/sharpening
* Object Localization

#### What is a convolution
* Concurrently applying a local function to parts of the image.
* Unlike pixelwise operations this looks at a <u> group</u> of pixels in a neighbourhood.
* [Convolution animations](https://towardsdatascience.com/intuitively-understanding-convolutions-for-deep-learning-1f6f42faee1)
* The way this function is applied to a single image is sometimes refered to as a <u>sliding window</u>, as the function only sees a small portion of the image at a time.
* Convolutions are trivially parallellised (one of the reasons for their success).

Take one row of an image and convolution combines this with a data structure that looks like a tiny image with one row and two columns containing two carefully chosen values:
|+1|-1|
|--|--|
This mini-array is known as:
* mask
* weight array
* operator
* kernel filter

To use it it is flipped to make a **template**.
|-1|+1|
|--|--|

Combine the template with the image and a combination of multiplication and addition.<br> Imagine sliding the mask (consisting of the collection of computations) along the input array, filling the output array as you slide along.
<br> Must be done for every row/line in the image and the results are assembled to form a new image.

#### Convolution Template - Horizontal Differencing:
|-1|+1|
|--|--|
Gives a first order approximation of the derivative of the image (in the x) using finite differences.
* Measures strength of vertical edges.
* Approximating the derivative at the point in-between 2 pixels.

N.B. These templates are not restricted to being 1D. Convolution function can operate on neighbours in both x and y.

### **Sobel Filter:**
Provides a mechanism for reducing noise:
| 1|0|1| 
|-|-|-|
|-2|0|2|
|-1|0|1|
#### Python Implementation:
```
from scipy.signal import convolve2d
sobel_x_kernel = [[-1,-,+1],[-2, 0, +2],[-1, 0, +1]]
sobel_x_img = convolve2d(img_gray ,sobel_x_kernel , ’same’)
``` 
"same" refers to the padding.
#### Sobel Filter in y:
To get gradients in the other direction transpose the filter:
|-1|-2|-1|
|:-:|:--:|:--:|
|0|0|0|
|1|2|1|

### **Average Filtering:**
Smooth out the image noise by performing a weighted average in a local area.
|$\frac{1}{9}$|$\frac{1}{9}$|$\frac{1}{9}$|
|:-:|:-:|:-:|
|$\frac{1}{9}$|$\frac{1}{9}$|$\frac{1}{9}$|
$\frac{1}{9}$|$\frac{1}{9}$|$\frac{1}{9}$|


What happens if we incease the size of the mask to e.g. 1 by 5:
|-1|-2|0|+2|+1|
|-|-|-|-|-|
**More surrounding data is used - effect of this depends on the numbers in the mask.**
<br> Combines smoothing and horizontal edge differencing.

### **Formula:**
* Given image $I$ and template $M$. <br> Ignore edge of image for now and suppose a template with an odd number of elements.
* M is represented by a ($2N+1$) x ($2N-1$) matrix$^1$ - these are indexed from $-N$ to $N$ so that the centre element of $M$ is $M$($0,0$).
* And so can write:
* $O(x,y) = M\star I(x,y) = \sum\limits^{N}_{i=-N}\sum\limits^{N}_{j=-N} M(i,j)I(x-i,y-i)$ <br> Where O is the output image.

$^1$ Can take any mask and pad with 0s to make square with an odd width.

### **Dealing with array boundaries:**
Possible methods:
* Make output array smaller than input - miss of a few rows and columns so mask never has to go over edge of the input array.
* "wrap around" the input array - right edge joins the left and the top joins the bottom. **Not** usually a good idea but fits in with a computational method of doing convolutions - **Fourier transform**.

* Assume input array surrounded by 0s.

* Assume left-hand column of input is repeated to the left as far as necessary, and the same for other boundaries.

* Assume data is reflected in the boundaries of the input array.

### **Convolution and Correlation**:
* Not the same thing.
* Convolution is a filtering operation - like detecting high frequency spatial content to expose edges.
    - Convolution flips the mask to produce a convolution template.
    - The reason for the flip is that masks can be convolved together to make new masks.
* Correlation looks looks at how similar two signals or patterns are: You do **NOT** flip the mask.