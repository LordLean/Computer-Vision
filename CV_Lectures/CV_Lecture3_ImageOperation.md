## Lecture 3

* Pixels - sampling of incoming light on a regular grid.
* Set of pixels that form an image are stored as an array.
* Typically original signal information is quantised to 8-bits per channel.
* Starts (0,0)  top left pixel $\rightarrow$ Ends (h-1,w-1)  bottom right pixel.

### **Images as Arrays:**
```
plt.imshow(image)

np.max(image) # Gives max
np.min(image) # Gives min
np.mean(image) # Gives average
np.std(image) # Gives standard deviation
```
This statistically calculations can of course be run on local patches of the image.

### **Histograms:**
Histograms split your data space into discrete <u>bins</u> (value ranges) and count the no. of occurences in each bin.

* Given a digital image with intensity values in range [0, L-1], its *histrogram* is a discrete funciton $h(r_k) = n_k$
    - $r_k$ is the $k$th intensity value 
    - $n_k$ is the number of pixels 

* Count pixels of intensity 0 - set $h(0)$ to that number.
* Count pixels of intensity 1 - set $h(1)$ to that number.
* etc.

Normalised Histograms:
* Divide $h(r_k)$ by the total number of pixels in an image.
* Sum of all histogram values = 1.
* Represents the probability that a pixel will have intensity $r_k$.

Image Histograms:
* Create $N$-intensity range bins - each pixel falls within one of the bins, and we plot the count of pixels in each bin.
```
plt.hist(img_gray.flattern(), normed = True, bins = 10)
```
* Append range of bins value to play around with histograms.

* These provide a description of all the contents of an image and throw away all of the spatial relationships (i.e. patterns/textures).

* Can still be used as features (differentiating images of sandy beaches from snowy hills).

Changing gray levels - play around with:
```
plot_im_hist(img_gray / 2)
``` 
Try multiplication/division
Or contrast:
```
plot_im_hist((img_gray - np.mean(img_gray)) * 2.0 + np.mean(img_gray))
``` 

Drawing image and histrogram:
``` 
def plot_im_hist(img_compressed):
    plt.subplot(1,2,1)
    plt.imshow(img_compresssed, cmap = "gray", vmin = 0, vmax = 255)
    plt.axis("off")
    plt.subplot(1,2,2)
    plt.hist(img_compressed.flatten(), np.linespace(0,255,20), normed = True)
    plt.show()
```

### **Non-linear changes to the grey-levels:**
* Can be useful to expand some parts of the grey-level scale at the expense of others. Raising the grey levels to a power is often used: A form of *gamma-correction*.
* $I_{out} = I_{in}^{\gamma}$
```
plot_im_hist(pow(img_gray, 1.1))
plot_im_hist(pow(img_gray, 1.0 /1.1))
```
### **Using the histogram to stretch the grey scale:**
Use a tailored curve from input to output to make output have a flat histogram: the same number of pixels at each grey level.
<br> This gives high visibility for most of the detail in the image.
```
cv2.equalizeHist()
```

### **Why Histogram Equalisation:**
* Doesnt necessarily make better looking photos but improves <u>global contrast</u>.

* Many more details may become visible in the image.

* Doesnt discriminate between image noise and true signal (can have adverse effects).

* Is invertible.

* Most as used in ML based vision systems but still used as a pre-processing step in traditional vision methods.

#### Thresholding - a **nonlinear** operation:
* Compares the gl (greyLevel) at each pixel with a number called the theshold.

* If the gl < theshold $\rightarrow$ corresponding pixel in the output is set to 1; otherwise 0.

* Might choose theshold by looking at histogram.

Often useful when applied to the output of some other image processing operation - rather than directly to the original image.

### Dynamic Range:
* The ratio between highest and lowest values that can be described.

* Photography - corresponds to the range of luminance visible in the image.

* Typically we are interested in detail within the middle range of luminance and can compress shadows and highlights.

* By playing with more localized contrast controls the appearance of a high dynamic range on screens and printed images can be created.

### Colour Images:
Colour represented in a variety of ways. Common approach is to split image to red, green and blue components.
<br> Image operations can be applied ot each of the colour planes separately or as a combination.
 
#### Colour Spaces:
* Several standardised colour spaces have been developed to represent images.
* RGB is most common.
* Also **hue saturation value** (HSV) and **LAB** distinguish between colour infromation and brightness (which encodes most of the structure).

### Hyperspectral Images:
* Conventional RGB colour camera divides the light spectrum into broad overlapping red, green and blue images slices that when combined appear realistic to the eye.

* Hyperspectral camera - effectively divides spectrum into many thin image slices.<br> Actual number depends on camera and application.
