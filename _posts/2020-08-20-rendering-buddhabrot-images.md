---
layout: post
title: Rendering Buddhabrot Images
---
render the probability distribution over the trajectories of points that escape the Mandelbrot fractal

**mandelbrot**      render traditional mandelbrot set  
**buddhabrot**      render buddhabrot (C implementation, 2x to 3x faster)  
**buddhabrotpp**    render buddhabrot (C++ implementation using complex.h)  
**buddhabrotmt**    render buddhabrot (multithreaded implementation, fastest)  
**mplshow**         display and save color PNG image from rendered image data  
**pilshow**         display and save grayscale PNG image from rendered image data  

![buddhabrot image](/assets/img/bb-i100k-s10M.png)

#### render `buddhabroti` image and save as intermediate file format
{% highlight sh %}
buddhabrot -w1024 -h1280 -i1000 -s10000000 images/bb-i1k-s10M
{% endhighlight %}

#### display and save image using the colormap viridis
{% highlight sh %}
mplshow -t -cviridis images/bb-i1k-s10M
{% endhighlight %}

#### display and save image clipping values above a specified value
{% highlight sh %}
mplshow -t -m250 images/bb-i1k-s10M
{% endhighlight %}

#### show and save image as a grayscale PNG
{% highlight sh %}
pilshow -t -m250 images/bb-ilk-s10M
{% endhighlight %}
