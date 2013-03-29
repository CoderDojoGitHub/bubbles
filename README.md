# Bubbles! And the power of Sketch.js

## Technologies

**sketch.js** is a tiny (~5k) boilerplate for creating JavaScript based creative coding experiments.

We'll also be using [coffeescript](http://coffeescript.org/) today to make our code easier to write (less lines!)

Lastly, we'll be using CodePen as our preferred in-browser code editor today. Because this project will utilize coffeescript, sketch.js, and jquery, CodePen will make it easier to configure during our relatively short lesson. If you want some extra credit when we're done, try getting everything to run locally!


## Preparing your environment

Today, we'll be drawing on a canvas and then creating some cool hover effects whenever your mouse touches different elements on the page. There will be a lot of room for experimentation so feel free to create some cool effects of your own!

### Creating a new Pen

Visit [codepen.io](http://codepen.io/) and click the "New Pen" button.

### Setting up the environment

In the top right corner of each section, there is a gear button that will allow you to access the settings for that piece of code. We're going to make a few modifications to make sure our environment is ready for the code we are about to write.

#### CSS Settings

Select "None" as the type of CSS.

Select "Reset" to reset style and elminiate browser inconsistancies.

Select "Prefix free" so you don't have to worry about vendor prefixes. This doesn't matter too much for what we are doing but if you start experimenting towards the end, it may help.

#### JavaScript Settings

Select "Coffeescript"

Select *Latest Version Of* **jQuery**

Link to the following external URL: https://raw.github.com/soulwire/sketch.js/master/js/sketch.min.js

## Creating a sketchpad

The first thing we want to do is create a place for our drawing.

In the CSS block we have to style our canvas and we'll give it a pretty background color.

CSS
```css
canvas {
  background: #023;
  display: block; 
}
``` 

JS
```coffeescript
# General Variables
sketch = Sketch.create()
```

At this point, you should see a colored background on the bottom half of your codepen screen.

### Creating Particles

Next, we're going to create a bunch of particles on the screen and have them move about randomly.

JS
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
  this.radius = this.baseRadius = 1
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

A lot just happened here! Try typing in each line by hand and thinking through what it may be doing. We are creating particles, defining their properties, drawing them on the screen, adjusting their speed, and more.

However, you may notice that the particles disappear after a while. After all, we only created 750. Let's find a way to recycle them.

JS
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

Now, let's make sure the JS is detecting where the mouse is.

JS
```coffeescript
# General Variables
sketch = Sketch.create()
particles = []
particleCount = 750
sketch.mouse.x = sketch.width / 2
sketch.mouse.y = sketch.height / 2
sketch.strokeStyle = 'hsla(200, 50%, 50%, .4)'
```

.... and a bit further down let's create an effect  for when the particles are close to the mouse.

JS
```coffeescript
Particle.prototype =
  update: ->
    # Determine Distance From Mouse
    distx = this.x - sketch.mouse.x
    disty = this.y - sketch.mouse.y
    dist = sqrt( distx * distx + disty * disty )
    
    # Set Radius
    if dist < this.threshold
      radius = this.baseRadius + ( ( this.threshold - dist ) / this.threshold ) * this.maxRadius
      this.radius = if radius > this.maxRadius then this.maxRadius else radius
    else
      this.radius = this.baseRadius
```


## Bonus Round!

Here are some extra challenges you can try.

1. Randomize the color of the particles.
2. Have your particles react to mouse movement (what if the mouse repels the nearest particles?).
3. Remove particles by clicking. 
4. Experiment!

In case you need some help....

[Sketch.js](http://intridea.github.com/sketch.js/)
[Coffeescript](http://coffeescript.org/)
[Sketch.js examples](http://soulwire.github.com/sketch.js/)






