---
title: "Intro Matplotlib, What is different in Axes and Figure ?"
date: 2017-06-28T17:44:03+08:00
draft: false 
---

Whenever we need to visualize some image, statistical chart, or even data in python, [Matplotlib](https://matplotlib.org/) the Python 2D plotting library, usually comes into mind.

Though we could just search for someone's code whenever we need plot some kinds of chart, such as histogram, piechart. However, I believed there are still a little bit concepts we could be familiar with at the beginning of learning Matplotlib library to help get more sense of it and be able to make a good tune to our chart whenever we use it.

The following explanation refer to [These general Concepts](http://matplotlib.org/faq/usage_faq.html#general-concepts).



### <strong>Everything in Matplotlib is organized in a hierarchy.</strong>



`Top-level`: the matplotlib's “state-machine environment” (provided by `matplotlib.pyplot`)

`Second-level`:  the first level of the object-oriented interface, in which pyplot is used only for a few functions such as figure creation.



> **Note: Official documatation recommands, for non-interactive plotting, it is suggested to use pyplot to create the figures and then the OO interface for plotting.**



According to hierarchy, there are three common comfusing objects: **figure**, **axes**, and **axis**.

In a nutshell, one `figure` object contains **one or more** `axes` objects,  and one `axes` object contains **at least two** `axis` objects.

Usually,  the axes objects within the figure are used for most plotting actions. 



The image below shows the componenet of a figure object.

![image from Matplotlib doc](http://matplotlib.org/_images/anatomy1.png)

The easiest way to create a new figure is :


```python
fig = plt.figure()   # an empty figure with no axes
# a figure with a 2x2 grid of Axes
fig, axes = plt.subplots(2, 2) 
```  
**Axes**: This is what you think of as 'a plot'.  

**Axis**: The number-line-like object which takes care of something like setting the data limits and generating the ticks. (the marks on the axis).

e.g.

```python
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

val = 3 + 5 * np.random.rand(3) # each 
pos = np.arange(3) + 0.5 # the bar centers on the y axis

labels = ['Tom', 'Tim', 'Sam']

# a figure with a 1x2 grid of Axes
fig, axes = plt.subplots(nrows=1, ncols=2, figsize=(10,3)) 
axes[0].barh(pos, val, align='center', color='b')
axes[0].set_yticks(pos)
axes[0].set_yticklabels(labels)

axes[1].bar(pos, val, align='center', color='r')
axes[1].set_xticks(pos)
axes[1].set_xticklabels(labels)
```

result:

![bar chart](img/ticks.png)
