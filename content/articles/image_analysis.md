Title: Image Analysis
Author: Kieran McAuley
Date: 2020-04-20
Category: blogging
Tags: sckit-image, scipy, image analysis, python

# ``Image Analysis using scipy and scikit-image``
In this post, I examine the the use of scipy and scikit-image for use in image analysis with a focus on its application to scientific data. 

## Images as numpy arrays
Images are represented in ``scikit-image`` using standard ``numpy`` arrays. This allows maximum inter-operability with other python libraries such as, ``matplotlib`` and ``scipy``.

A standard color image is a 3D array, where the last dimension has a size of 3 and contains information on the images red, grenn and blue channels (RGB). The following exmaple uses a sample image from the sckit-image module to demonstrate the attributes of an image array.

      :::python3
     # loading standard libraries
     import numpy as np
     from matplotlib import pyplot as plt
     from skimage import data

     cat = data.chelsea()
     # obtain the image dimensions 
     print("Shape:", cat.shape)
     #obtaining the maximum array values
     print("Values min/max:", cat.min(), cat.max())
     
     # in python we can manipulate the values of the image arrays
     # here a square portion is defined using the row and column co-ordinates of the image
     # and the channel data is replaced with the darkest red shade available 
     # while setting the green and bluw values to zero
     cat[10:110, 10:110, :] = [255, 0, 0]  # [red, green, blue]
     # show the image array
     plt.imshow(cat);

The output of this code describes:

The **shape** of the 3D array, we can inspect the number of rows and columns, as well as the number of color channels. Some image formats can contain additional channel filter data i.e infrared, etc. producing greater values.
Shape: (300, 451, 3)

There exist different conventions for representing image values, the most common are:

_Float64_: where  0 is black, 255 is white (0-255)

_uint8_: where  0 is black, 1 is white (0-1)

Scipy allows any data-type as input, as long as the range is correct (**0-1** for floating point images, **0-255** for unsigned bytes, **0-65535** for unsigned 16-bit integers).

The __range__ of values contained in this image were found to be: 
Values min/max: 0, 231
It is clear this image was imput as an float64 data type,this could be easily converted if required.

The resulting plot is displyed below:  

![cat]({static}/img/cat.png)  
  

## Separation of color channels
---
Using another sample image the separation of color can be performed quite simply, this may be useful in assessing the different channels of irraiated film using a pyton code.


    :::python3
     image = plt.imread('../images/Bells-Beach.jpg')

     # each color channel assigned to a different variable

     r = image[:, :, 0] # 0 channel is red
     # ... operator in array grabs all values until final array dimension
     g = image[..., 1] # 1 is green
     b = image[..., 2] # 2 is blue

     # plotting the image and r, g, b channels
     # setting figure size 
     f, axes = plt.subplots(1, 4, figsize=(16, 5))

     for ax in axes:
     ax.axis('off')

     (ax_r, ax_g, ax_b, ax_color) = axes
     
     # set image to grey scale to view color dependency
     ax_r.imshow(r, cmap='gray')
     ax_r.set_title('red channel')

     ax_g.imshow(g, cmap='gray')
     ax_g.set_title('green channel')

     ax_b.imshow(b, cmap='gray')
     ax_b.set_title('blue channel')

     # stack the R, G, and B layers using a numpy attribute
     ax_color.imshow(np.stack([r, g, b], axis=2))
     ax_color.set_title('all channels');

The resulting plots are displayed below:  
  
![separation]({static}/img/separation.png)  
  
The lighter the areas in the individual images represent areas with the highest value for the corresponding channel. For example, the sea in the blue channel is quite bright as it holds a large portion of the information for that section. Where as the bushes are quite dark due to the values being contained in mainly the green channel.  

## Segmentation of images 
---
Segmentation deals with separating the image into regions of interest. 
Segmentation contains two major sub-fields:

*Supervised segmentation*: Some prior knowledge, possibly from human input, is used to guide the algorithm.

*Unsupervised segmentation*: No prior knowledge. These algorithms attempt to subdivide into meaningful regions automatically. The user may be able to tweak settings like number of regions.

The simplest method would be thresholding, which will be examined in this section.  

    :::python3
     import numpy as np
     import matplotlib.pyplot as plt
 
     # importing relevant packages 
     import skimage.data as data
     import skimage.segmentation as seg
     from skimage import filters
     from skimage import draw
     from skimage import color
     from skimage import exposure
 
     # useful function when plotting multiple figures
     def image_show(image, nrows=1, ncols=1, cmap='gray', **kwargs):
        fig, ax = plt.subplots(nrows=nrows, ncols=ncols, figsize=(16, 16))
        ax.imshow(image, cmap='gray')
        ax.axis('off')
        return fig, ax
     
     # loading sample image
     text = data.page()
     image_show(text);

![threshold text]({static}/img/thresh_pic.png)  

The values of the array can be unraveled to generate a histogram of the individual pixel values. If the image was exposed correctly the distribution may be bimodal allowing us to simply cut-off the lower values. However, this is usually not the case.  

![histogram]({static}/img/hist.png)  

Using supervised segmentation, we could set a cut-off value. This would require trial and error, therefore, a supervised method can be more benficial. In this case the sauvola filter was applied which attempts to set an ideal threshold for every pixel.  

After examining multiple cut-off values the best supervised thresholding set at 100 produced the following figure:  

![supervised]({static}/img/supervised.png)  

Clearly there is room for improvement. By using the sauvola filter in sckit-image pakage, the following image was obtained.  

![unsupervised]({static}/img/unsupervised.png)  

The choice of an appropriate filter had a substantial impact on the information retained in this image in comparision to the supervised method.

## Conclusions  
---  
python can be used as a powerful tool in the anaylsis of images, in particular with the inclusion of the filters and attributes in the scipy and sckit-image modules. 




