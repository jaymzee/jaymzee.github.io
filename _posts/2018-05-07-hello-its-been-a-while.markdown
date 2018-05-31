---
layout: post
title: "Hello, pygame is pretty cool"
---
# Graphics programming in python using pygame
At vero eos et accusamus et iusto odio dignissimos ducimus qui blanditiis
praesentium voluptatum deleniti atque corrupti quos dolores et quas molestias
excepturi sint occaecati cupiditate non provident, similique sunt in culpa qui
officia deserunt mollitia animi, id est laborum et dolorum fuga.

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

