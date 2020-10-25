---
layout: post
title: Rendering Buddhabrot Images
---
A Buddhabrot is an image of the probability distribution over the
trajectory of points that escape the Mandelbrot set using a technique
discovered by Melinda Green.  While a traditional Mandelbrot
image is rendered by coloring the pixel based on how many iterations of
*z → z<sup>2</sup> + c* it takes for _z_ to grow toward infinity, a Buddhabrot
takes a very different approach.

To render a Buddhabrot, a random value for _z<sub>i</sub>_ is chosen, then
*z → z<sup>2</sup> + c* is repeated while incrementing a counter at each
_z<sub>i</sub>_ only if this sequence of _z<sub>i</sub>_ is known to escape to infinity.
This array of counters for each _z<sub>i</sub>_ is the probability distribution
described above.

![buddhabrot image](/assets/img/bb-i100k-s10M.png)

To render your own images, download the buddhabrot repository and build it
or install a release for your os.
Then you may use the tools to render and display the images as demonstrated
below.

#### render buddhabrot and save data to file
{% highlight sh %}
buddhabrot -w1024 -h1280 -i1000 -s10000000 images/bb-i1k-s10M
{% endhighlight %}

#### display buddhabrot data and save as an image using the colormap viridis
{% highlight sh %}
mplshow -t -cviridis images/bb-i1k-s10M
{% endhighlight %}

#### display buddhabrot data and save image clipping values above a specified value
{% highlight sh %}
mplshow -t -m250 images/bb-i1k-s10M
{% endhighlight %}

#### display buddhabrot data and save to a grayscale PNG image clipping values
{% highlight sh %}
pilshow -t -m250 images/bb-ilk-s10M
{% endhighlight %}
