---
layout: post
title: "Playing with processing"
---
Processing makes it very easy to make applications with graphics. 
A simple hello world type app is as minimal as follows:

{% highlight java %}
size(300, 300);
rect(100, 100, 50, 50);
text("Hello world", 10, 10);
{% endhighlight %}

<script src="https://cdnjs.cloudflare.com/ajax/libs/processing.js/1.4.8/processing.min.js"></script>
<script type="text/processing" data-processing-target="pjs">
size(300, 300);
rect(100, 100, 50, 50);
text("Hello world", 10, 20);
</script>
<canvas id="pjs">Your browser doesn't support the canvas element</canvas>