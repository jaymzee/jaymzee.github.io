---
layout: post
title: "Hello, pygame is pretty cool"
---
# Graphics programming in python using pygame

{% highlight python %}
import sys
import pygame

size = (400,400)
green = (0,255,0)
rect = (100,100,50,25)
pygame.init()
surf = pygame.display.set_mode(size)
pygame.draw.rect(surf,green,rect,1)
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
{% endhighlight %}

