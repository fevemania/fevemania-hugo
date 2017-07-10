+++
title = "Global Histogram Equalization"
date = 2017-06-28T15:08:50+08:00
draft = false
tags = ["OpenCV"]
+++

What **Histogram Equalization** does is to improve the contrast of the image.

![hist](http://docs.opencv.org/3.1.0/histogram_equalization.png)

The picture above shows distribution of pixel intensity in grayscale.

**x-axis** : The intensity value ranged from 0 - 255.</br>
**y-axis** : The number of pixel given the particular intensity value.



**pros**: be useful in images with backgrounds and foregrounds that are both bright or both dark.

**cons**: comes along with a side-effect which may indiscriminately increase the contrast of background noise.

---

Before introduce histogram equlization, here's a question. </br>
**What is the difference between Histogram Equalization and Normalization ?** Aren't they both do something like stretch out the range of pixel intensity distribution?

This [link](http://sebastianraschka.com/Articles/2014_about_feature_scaling.html#z-score-standardization-or-min-max-scaling) has the comprehensive comparison about Histogram Equalization and contract strectching (also named as Normalization)

---

Ok, Let's dive into how global histogram equalization works.

Take the grayscale picture below as an instance:



+ **cdf (x)** :  the cumulative number of pixels with intensity below x.


+ **Max_hist** :  Max intensity of whole picture. (that is, the highest value of the histogram)
+ **Max_cdf**   :  Max cdf value of whole cdf distribution.

In order to show the trend of cdf relative to intensity value, we need to normalize cdf with

<div>$$ cdf\_normalize = \frac{Max\_hist * cdf(x)} {Max\_cdf} $$</div>



![hist][hist]
![result][result]

[hist]: img/global_hist_equalize/hist.png
[result]: img/global_hist_equalize/before_after.png



*Note:  You can apply histogram both on grayscale (1-channel) and RGB image (3-channels). For RGB image case, you should first convert RGB colorspace to other colorspace such as YUV which can seperate luminance signal (Y) and chrominance signal (U, V). Cause you may just want contrast and brightness to be adjust, not on color.*



For more application details, please see [Wikipedia of Histogram equalization](https://en.wikipedia.org/wiki/Histogram_equalization
).

For implementation on Numpy or OpenCV, look at [OpenCV documentation](http://www.ques10.com/p/5967/compare-between-contract-stretching-and-histogra-1/).

