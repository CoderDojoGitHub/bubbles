# Bubbles! And the power of Sketch.js

## Technologies

**sketch.js** is a tiny (~5k) boilerplate for creating JavaScript based creative coding experiments.

We'll also be using [coffeescript](http://coffeescript.org/) today to make our code easier to write (less lines!)

Lastly, we'll be using CodePen as our preferred in-browser code editor today. Because this project will utilize coffeescript, sketch.js, and jquery, CodePen will make it easier to configure during our relatively short lesson. If you want some extra credit when we're done, try getting everything to run locally!


## Preparing your environment

Today, we'll be drawing on a canvas and then creating some cool hover effects whenever your mouse touches different elements on the page. There will be a lot of room for experimentation so feel free to create some cool effects of your own!

### Creating a new Pen

Visit [codepen.io](http://codepen.io/) and click the "New Pen" button.

![](http://i.imgur.com/0zk5K1C.png)

### Setting up the environment

In the top right corner of each section, there is a gear button that will allow you to access the settings for that piece of code. We're going to make a few modifications to make sure our environment is ready for the code we are about to write.


#### CSS Settings

- Select "None" as the type of CSS.
- Select "Reset" to reset style and elminiate browser inconsistancies.
- Select "Prefix free" so you don't have to worry about vendor prefixes.
  - This doesn't matter too much for what we are doing but if you start experimenting towards the end, it may help.

![](http://i.imgur.com/u065kVg.png)

#### JavaScript Settings

- Select "Coffeescript"
- Select *Latest Version Of* **jQuery**
- Link to the following external URL: https://raw.github.com/soulwire/sketch.js/master/js/sketch.min.js

![](http://i.imgur.com/HGXwgrQ.png)

## Creating a sketchpad

The first thing we want to do is create a place for our drawing.

In the CSS block we have to style our canvas and we'll give it a pretty background color.

**CSS**
```css
canvas {
  background: #023;
  display: block; 
}
``` 

**JS**
```coffeescript
# General Variables
sketch = Sketch.create()
```

At this point, you should see a colored background on the bottom half of your codepen screen.

### Creating Particles

Next, we're going to create a bunch of particles on the screen and have them move about randomly.

**JS**

This define hat we initially want

- A new sketchboard
- An ampty list of particles
- Total 750 particles
- Bluish Strokestyle (border color)

```coffeescript
# General Variables
sketch = Sketch.create()
particles = []
particleCount = 750
sketch.strokeStyle = 'hsla(200, 50%, 50%, .4)'
```

This describes what a particle is:

- x: its horizontal location (randomly assigned)
- y: its vertical location (randomly assigned)
- vx: horizontal velocity (0 means still)
- vy: vertical velocity (randomly chosen in the negative direction between 1 and 10 then divided by 5)

```coffeescript
# Particles
Particle = ->
  this.x = random( sketch.width ) 
  this.y = random( sketch.height, sketch.height * 2 )
  this.vx = 0
  this.vy = -random( 1, 10 ) / 5
  this.radius = 1
  this.baseRadius = 1
  this.maxRadius = 50  
  this.threshold = 150 
```

These are the behaviour functions for the particle:

**update**

Describes what that bubble is suppose to do

- Increases vx (horizontal speed) randomly
- Increases vy (vertical speed) randomly
- Changes x (horizontal location) randomly
- Changes y (vertical location) randomly

**render**

- Hands over the bubble to sketch.js
- Sketch.js creates an arc that the particle follos
- Sketch.js fills in the bubble with color
- Sketch.js adds a border stroke to the bubble

```coffeescript
# Particle Prototype
Particle.prototype =
  update: ->
    # Adjust Velocity
    this.vx += ( random( 100 ) - 50 ) / 1000
    this.vy -= random( 1, 20 ) / 10000
      
    # Apply Velocity
    this.x += this.vx
    this.y += this.vy
    
  render: ->
    sketch.beginPath()
    sketch.arc( this.x, this.y, this.radius, 0, TWO_PI )
    sketch.closePath()
    sketch.fill()
    sketch.stroke()
```

Now we start populating the bubbles

- Variable particles is currently empty
- We do a while loop from 750 to 0 and add a new particle to the particles list
- Then we clear the visible sketchboard (like resetting it)
- We do another while loop where we go through all the bubbles and `update()` (assign animation functions)
- Then we do our last while loop and render each particle to come on screen

```coffeescript
# Create Particles
z = particleCount
while z--
  particles.push( new Particle() )
  
# Sketch Clear
sketch.clear = ->
  sketch.clearRect( 0, 0, sketch.width, sketch.height )
  
# Sketch Update
sketch.update = ->
  i = particles.length
  particles[ i ].update() while i--

# Sketch Draw
sketch.draw = ->  
  i = particles.length
  particles[ i ].render() while i--
```

A lot just happened here! Try typing in each line by hand and thinking through what it may be doing. We are creating particles, defining their properties, drawing them on the screen, adjusting their speed, and more.

However, you may notice that the particles disappear after a while. After all, we only created 750. Let's find a way to recycle them.


### Working prototype - Full Code


```coffeescript
# General Variables
sketch = Sketch.create()
particles = []
particleCount = 750
sketch.strokeStyle = 'hsla(200, 50%, 50%, .4)'

# Particles
Particle = ->
  this.x = random( sketch.width ) 
  this.y = random( sketch.height, sketch.height * 2 )
  this.vx = 0
  this.vy = -random( 1, 10 ) / 5
  this.radius = 1
  this.baseRadius = 1
  this.maxRadius = 50  
  this.threshold = 150

# Particle Prototype
Particle.prototype =
  update: ->
    # Adjust Velocity
    this.vx += ( random( 100 ) - 50 ) / 1000
    this.vy -= random( 1, 20 ) / 10000
      
    # Apply Velocity
    this.x += this.vx
    this.y += this.vy
    
  render: ->
    sketch.beginPath()
    sketch.arc( this.x, this.y, this.radius, 0, TWO_PI )
    sketch.closePath()
    sketch.fill()
    sketch.stroke()

# Create Particles
z = particleCount
while z--
  particles.push( new Particle() )
  
# Sketch Clear
sketch.clear = ->
  sketch.clearRect( 0, 0, sketch.width, sketch.height )
  
# Sketch Update
sketch.update = ->
  i = particles.length
  particles[ i ].update() while i--

# Sketch Draw
sketch.draw = ->  
  i = particles.length
  particles[ i ].render() while i--
```

![](http://i.imgur.com/v2YfLb9.png)


## Recycling particles

You may notice that the particles disappear from the screen after a few seconds, so now we will detect if they are outside
our canvas area and put them back on the screen.

- Scroll up to the Particle update function
- Add the new code (5 lines) that checks bounds


**JS**
```coffeescript
# Particle Prototype
Particle.prototype =
  update: ->
    # Adjust Velocity
    this.vx += ( random( 100 ) - 50 ) / 1000
    this.vy -= random( 1, 20 ) / 10000
      
    # Apply Velocity
    this.x += this.vx
    this.y += this.vy


    # **New code**
    # Check Bounds   
    if this.x < - this.maxRadius || this.x > sketch.width + this.maxRadius || this.y < - this.maxRadius
      this.x = random( sketch.width )      
      this.y = random( sketch.height + this.maxRadius, sketch.height * 2 )
      this.vx = 0
      this.vy = -random( 1, 10 ) / 5



  render: ->
    sketch.beginPath()
    sketch.arc( this.x, this.y, this.radius, 0, TWO_PI )
    sketch.closePath()
    sketch.fill()
    sketch.stroke()
```


## Fancy customizations!

### Increase particles

```coffeescript
particleCount = 1234
```

### Increase the radius

```coffeescript
this.radius = 10
this.baseRadius = 10
```

![](http://i.imgur.com/umJGIY4.png)

### Randomize particle color

```coffeescript
Particle = ->
  this.x = random( sketch.width ) 
  this.y = random( sketch.height, sketch.height * 2 )
  this.vx = 0
  sketch.fillStyle = 'hsla('+random(200)+', 50%, 50%, .4)' # new line
  this.vy = -random( 1, 10 ) / 5
  this.radius = 10
  this.baseRadius = 10
  this.maxRadius = 50  
  this.threshold = 150
```

![](http://i.imgur.com/SvvtWSh.png)

## Bonus Round!

Here are some extra challenges you can try.

1. Have your particles react to mouse movement (what if the mouse repels the nearest particles?).
2. Remove particles by clicking. 
3. Experiment!

In case you need some help....

[Sketch.js](http://intridea.github.com/sketch.js/)
[Coffeescript](http://coffeescript.org/)
[Sketch.js examples](http://soulwire.github.com/sketch.js/)






