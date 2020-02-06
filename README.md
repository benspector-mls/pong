# Setup

To install this project, first clone the [template](https://github.com/benspector3/asd-template/) repository by entering these commands into your bash terminal:

```bash
git clone https://github.com/benspector3/asd-template.git
rm -rf asd-template/.git
```

Then, rename the folder to `pong`

**Table of Contents**
- [Learning Objectives](#learning-objects)
- [Abstraction Example](#abstraction-example)
- [Planning](#planning)
- [Plan of Attack](#plan-of-attack)
- [Helpful code](#helpful-code)

# Learning Objectives
- Practice modeling data with Objects
- Reuse code from previous projects to create something new
- Practice abstraction
- Apply the algorithm for detecting collisions between objects

# Abstraction Example	

This project will have you practicing reusing code from prior projects to create something new. In fact, almost all of the code needed to create Pong has already be written _by you_ in prior projects (Bouncing Box / Walker). 

However, in those projects we were only dealing with a single object's movement. In this project we will need to manage the movement of (and all associated data for) 3 objects: the ball and the two paddles. Provided for you in this README is some code that we've written in the past that will be applicable to this project. 

However, to make the code reusuable for multiple objects, you will need to refactor the code. This process is called **Abstraction**:

> Abstraction is the process of turning something specific (hard-coded) into something generic (reusable)	

To refactor repetitive code for abstraction, you can follow these 3 steps:	
1. identify the repetetive statements and turn those statements into a new function declaration	
2. identify the changing expressions/data (if any) and turn those expressions/data into parameters	
3. replace repetitive code with function calls	

Below is an example of refactoring for abstraction. Consider the following code which simulates rolling dice of different sizes:	

```js
var roll1 = Math.ceil(Math.random() * 6);	
var roll2 = Math.ceil(Math.random() * 10);	
var roll3 = Math.ceil(Math.random() * 20);	
```

Each time I roll the dice I am using the `Math.ceil()` and `Math.random()` functions, the `*` operator and a number value. These statements can be turned into a new function declaration.	

```js	
function rollDice() {	
  return Math.ceil(Math.random() * 6); // the 6 should be a parameter, not hard-coded	
}	
var roll1 = rollDice(6);	
var roll2 = rollDice(10);	
var roll3 = rollDice(20);	
```

However, we want the number value `6` to change each time we call the function. That value must be replaced with a parameter:	

```js	
function rollDice(sides) {	
  return Math.ceil(Math.random() * sides);	
}	
var roll1 = rollDice(6);	
var roll2 = rollDice(10);	
var roll3 = rollDice(20);	
```

# Planning

Always start any programming task by clarifying what you want to do and then breaking it down into small steps. Small steps can get you just about anywhere if you’ve got enough time. If you get stuck, break it down smaller!

With your partner, consider each of these questions and make sure you are aligned on your answers:

### User Story / Gameplay
- Describe the gameplay
- What are the conditions when the game begins? 
- Does the game have an end? If so, what are the conditions for when it ends?
- What `if`s will there be?

### Visual Game Components:
- What are the visual game components? For example, in Bouncing Box, the game components were the board and the box.
  - Which will be static? (the board)
  - Which will be animated? (the box)
- What data will you need to manage each game component? For example, in Bouncing Box, the data values were `positionX`, `speedX`, and `points`.

### Events / Logic 
- What events will occur in this game? (timer events, keyboard events, clicking events?)
- For each "event", write out the high-level logic of what will happen. It is better (and tricky) to be as specific as you can while remaining high-level!

**Bouncing Box Example:**

1. When the user clicks on the box --> the score increases and is displayed on the box, the speed increases, the box is reset to the starting position. 
2. When the timer ticks --> the box will move forward in some direction, if it hits the edge of the screen, reverse the direction 

# Plan of Attack

I recommend that you complete this project by completing these tasks:
1. Create the DOM elements needed for the game with HTML and CSS
2. Create a factory function to structure the data needed for each DOM element.
3. Create a helper function for repositioning the DOM elements on the screen using jQuery
4. Move the paddles in response to keyboard events
5. Move the ball in response to timed events
6. Identify when the ball collides with the paddles --> Determine how the ball will bounce off
7. Identify when the ball collides with the top or bottom --> Determine how the ball will bounce off
7. Identify when a point ends --> Determine what to do to start a new point
8. End the game when 11 points are reached

# Helpful Code

Below is some code we've written in the past that may be helpful to you in this project. You will likely have to refactor the code to make it more abstract (reusable).

### HTML for Game Items:

Open the `index.html` file. You should see this in the body:

```html
<body>
  <div id='board'>
    <div id='gameItem'></div>
  </div>
</body>
```

Each project in this class will be build on some kind of `board` with various `gameItems` that are on the board. For this project, there are only 3 game items:
- the left paddle
- the ball
- the right paddle

Each one of these game items needs to be represented in HTML and, for the most part, `<div>`s can be used. To create a `<div>` with a particular `id` attribute, place the `id=""` attribute inside the opening tag:

```html
<div id="uniqueGameItemName"> </div>
```

### CSS for Game Items

Open the `index.css` file.

Adding CSS makes our gameItems actually become visible. For all projects in this course, we'll be using simple 2D shapes since they are relatively easy to render with basic HTML and CSS skills.

The following properties will be useful for determining the appearance of our DOM elements:
- `background-color`: the color of the element
- `width`: the width of the element in pixels
- `height`: the height of the element in pixels
- `border-radius`: how rounded the edges of the element are. Leaving out this property will leave the element as a rectangle. Setting this value to be equal to `width` and `height` will make the shape a circle.

The following properties will allow us to place our elements anywhere on the screen, relative to the `board`.
- `position: absolute`: allows us to use the `top` and `left` properties to position HTML elements anywhere we want on the screen relative to the parent element. 
- `top`: the y-coordinate location of the element on a flipped y-axis (value increases as you move downwards).
- `left`: the x-coordinate location of the element.

Overall, the CSS should look like this:

```css
#id {
  /* appearance */
  background-color: white;
  width: 20px;
  height: 20px;
  border-radius: 20px;
  
  /* positioning */
  position: absolute;
  top: 100px;
  left: 100px;
}
``` 

Suggestions for this project:
- Each paddle should have a unique `background-color`
- Both paddles should have `width: 20px;` and `height: 80px;`
- The ball should have `width:20px;`, `height:20px` and `border-radius: 20px;`

### Factory Function

We will need to manage the data for each `gameItem` in this project. I highly recommend using a factory function to ensure that each `gameItem` has the data below:
- `gameItem.$element`
- `gameItem.x`
- `gameItem.y`
- `gameItem.velocityX`
- `gameItem.velocityY`

```js
function carFactory(make, color, doors = 4) {
  return {
    "make": make,
    "color": color,
    "doors": doors
  };
}

var myCar = carFactory("chevy", "black", 2);
var parentsCar = carFactory("volvo", "silver");   // default doors = 4 used 
var dreamCar = carFactory("ferrari", "red", 2);
```

### Repositioning DOM Elements

This function can be used to reposition a DOM element `$gameItem` at an absolute position on the screen by manipulating the CSS properties `left` and `top`. 

This function assumes that we have the following _global_ data values:
- `$gameItem`: the jQuery Object for a `<div id="gameItem">` element
- `x`: the x-coordinate / `left` value of the `$gameItem`
- `y`: the y-coordinate / `top` value of the `$gameItem`
- `velocityX`: the velocity (pixels/frame) along the x-axis of the `$gameItem`
- `velocityY`: the velocity (pixels/frame) along the y-axis of the `$gameItem`

```js
function moveGameItem() {
  x += velocityX;
  y += velocityY;
  $gameItem.css("left", x);
  $gameItem.css("top", y);
}
```

We will need to refactor this function such that it can handle _any_ `$gameItem` with its own `x`, `y`, `velocityX`, and `velocityY` values. 

### Keybord Inputs

This function assumes that the event `"keydown"` is being listened for. You can change what events are being listend for in the function `turnOnEvents` of the template. 

What your program does in response to particular keys is up to you. Check out the [Walker project](https://github.com/benspector3/asd-template-keyboard-intro/) for ideas on how to move an object with your keyboard.

```js
var KEYCODE = {
  ENTER: 13,
}

function handleKeyDown() {
  var keycode = event.which;
  console.log(keycode);
  
  if (keycode === KEYCODE.ENTER) {
    console.log("enter pressed");
  }
}
```

Use https://keycode.info/ to find out the keycode for any key. 
