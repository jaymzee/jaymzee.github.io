---
layout: post
title: asteroid game
---



#Calculating line segment intersections for collision detection
To test two line segments for intersection, create a parametric equation for each line. We'll use s for the first line and t for the second. Both s and t run from 0 to 1 to give us points along our line segments. Solving the system of equations using determinants will give values of s and t for where the lines intersect. Therefore if both s and t are within 0 and 1, then the line segments intersect.

{% highlight java %}
float det = (x2-x1)*(y3-y4) - (y2-y1)*(x3-x4);
if (det != 0) {
    float s = ((x3-x1)*(y3-y4) - (y3-y1)*(x3-x4)) / det;
    float t = ((y3-y1)*(x2-x1) - (x3-x1)*(y2-y1)) / det;
    if (s > 0 && s < 1 && t > 0 && t < 1) {
        // lines intersect
    }
}
{% endhighlight %}

<script src="https://cdnjs.cloudflare.com/ajax/libs/processing.js/1.4.8/processing.min.js"></script>
<script type="text/processing" data-processing-target="pjs">
boolean keyLeft;
boolean keyRight;
boolean keyFire;
boolean keyThrust;

Ship ship;
ArrayList<Asteroid> asteroids;

void setup() {
  size(320, 480);
  stroke(255);
  noFill();
  ship = new Ship(width/2, height/2);
  asteroids = new ArrayList<Asteroid>();
  
  for (int i = 0; i < 10; i++) {
    asteroids.add(new Asteroid());
  }
}

void draw() {
  background(0);
  if (keyLeft) {
    text("left", 10,10);
    ship.rotateLeft();
  }
  if (keyRight) {
    text("right", 30,10);
    ship.rotateRight();
  }
  if (keyThrust) {
    text("thrust", 55,10);
    ship.velocity.x += .01 * cos(ship.heading);
    ship.velocity.y += .01 * sin(ship.heading);    
  }
  if (keyFire) {
    text("fire", 90,10);
  }
    
  ship.draw();
  for (Asteroid asteroid : asteroids) {
    asteroid.draw();
  }
  
  ArrayList<Asteroid> hitones = new ArrayList<Asteroid>();
  for (Asteroid asteroid : asteroids) {
    if (asteroid.hit) {
      hitones.add(asteroid);
    }
  }
  for (Asteroid asteroid : hitones) {    
    asteroids.remove(asteroid);
    if (asteroid.size > 1) {
      Asteroid a1 = new Asteroid(asteroid.location.x, 
                                 asteroid.location.y, 
                                 asteroid.size - 1);
      Asteroid a2 = new Asteroid(asteroid.location.x, 
                                 asteroid.location.y, 
                                 asteroid.size - 1);
      asteroids.add(a1);
      asteroids.add(a2);
    }
  }
    
  text("a and d to turn the ship, s for thrust, and space bar to fire", 10, 25);
}

void keyPressed() {
  switch(key) {
  case 'a': 
    keyLeft = true;
    break;
  case 'd': 
    keyRight = true;
    break;
  case ' ': 
    if (!keyFire) {
      ship.fire();
      keyFire = true;
    }
    break;
  case 's': 
    keyThrust = true;
    break;
  default:
    break;
  }
}

void keyReleased() {
  switch(key) {
  case 'a': 
    keyLeft = false;
    break;
  case 'd': 
    keyRight = false;
    break;
  case ' ': 
    keyFire = false;
    break;
  case 's': 
    keyThrust = false;
    break;
  default:
    break;
  }
}


class Asteroid {
  PVector location;
  PVector velocity;
  float angle;
  float angularVelocity;
  int size;
  boolean hit;
  
  private float r[] = new float[15];
  private float t[] = new float[15]; 
  
  Asteroid() {
    this(random(0, width-1), random(0, height-1), 3);
  }
  
  Asteroid(float x, float y, int size) {
    location = new PVector();
    velocity = new PVector();
   
    location.x = x;
    location.y = y;
    this.size = size;
    velocity.x = random(-2, 2);
    velocity.y = random(-2, 2);
    angularVelocity = random(-.05, .05);    
    initShape();
  }
  
  void initShape() {
    for (int i = 0; i < r.length; i++) {
      r[i] = 10 * random(size, size + 1);
      t[i] = random(2 * PI);      
    }
    //sort the list
    for (int i = 0; i < t.length; i++) {
      for (int j = i + 1; j < t.length; j++) {
        if (t[i] > t[j]) {
          float temp = t[j];
          t[j] = t[i];
          t[i] = temp;
        }
      }
    }
  }
  
  void draw() {
    physics();
    pushMatrix();
    translate(location.x, location.y);
    beginShape();
    rotate(angle);    
    for (int i = 0; i < r.length; i++) {      
      vertex(r[i] * cos(t[i]),
             r[i] * sin(t[i]));
    }    
    endShape(CLOSE); 
    popMatrix();
  }
  
  void physics() {    
    location.x += velocity.x;
    location.y += velocity.y;
    angle += angularVelocity;
        
    if (location.y < 0) {
      location.y += height;
    }
    if (location.y > height) {
      location.y -= height;
    }
    if (location.x < 0) {
      location.x += width;
    }
    if (location.x > width) {
      location.x -= width;
    }
    //check for hit from ship
    if (ship.firecount > 0) {
      for (int i = 0; i < r.length; i++) {
        int n = (i + 1) % r.length;
        float x1 = location.x + r[i] * cos(angle + t[i]);
        float y1 = location.y + r[i] * sin(angle + t[i]);
        float x2 = location.x + r[n] * cos(angle + t[n]);
        float y2 = location.y + r[n] * sin(angle + t[n]);
        float x3 = ship.location.x;
        float y3 = ship.location.y;
        float x4 = x3 + 200 * cos(ship.heading);  
        float y4 = y3 + 200 * sin(ship.heading);
        float det = (x2-x1)*(y3-y4) - (y2-y1)*(x3-x4);
        if (det != 0) {
          float s = ((x3-x1)*(y3-y4) - (y3-y1)*(x3-x4)) / det;
          float t = ((y3-y1)*(x2-x1) - (x3-x1)*(y2-y1)) / det;
          if (!hit && s > 0 && s < 1 && t > 0 && t < 1) {
            hit = true;
            ship.firecount = 0;
            break;            
          }
        }
      }
    }
  }
}

class Ship {
  PVector location;
  PVector velocity;
  float heading;
  
  int firecount;
  
  Ship(float x, float y) {
    location = new PVector(x, y); 
    velocity = new PVector();
    heading = -PI/2.0;
  }
  
  void rotateRight() {
    ship.heading += .1;
  }
  
  void rotateLeft() {
    ship.heading -= .1;
  }
  
  void fire() {
    if (firecount == 0) {
      firecount = 20;
    }
  }
  
  void physics() {    
    location.x += velocity.x;
    location.y += velocity.y;
    velocity.x *= .999;
    velocity.y *= .999;
    if (!keyThrust && velocity.mag() < .1) {
      velocity.x = 0;
      velocity.y = 0;
    }
    if (location.y < 0) {
      location.y += height;
    }
    if (location.y > height) {
      location.y -= height;
    }
    if (location.x < 0) {
      location.x += width;
    }
    if (location.x > width) {
      location.x -= width;
    }
  }
  
  void draw() {
    physics();
    pushMatrix();
    translate(location.x, location.y);
    rotate(heading + PI/2);
    line(-5, 6, 0, -6);
    line(0, -6, 5, 6);
    line(4, 4, -4, 4);
    
    if (keyThrust) {
      line(-1, 4, -2, 7);
      line(1, 4, 2, 7);
    }

    if (firecount > 0) {
      for (int i=0; i < 10; i++) {
        point(0, -20 * i + firecount - 20);        
      }      
      --firecount;
    }    
    popMatrix();
  }
}
</script>
<script>
window.addEventListener("load", function() {
  document.getElementById("pjs").focus();    
}, false);
</script>

<canvas id="pjs">Your browser does not support the canvas element</canvas>