# Pong

<img src="img/pong.gif">

**Table of Contents**
- [Setup](#setup)
- [Learning Objectives](#learning-objectives)
- [Planning](#planning)
- [Plan of Attack](#plan-of-attack)
- [Helpful Code](#helpful-code)
  - [HTML for Game Items](#html-for-game-items)
  - [CSS for Game Items](#css-for-game-items)
  - [Use Objects to Manage Data](#use-objects-to-manage-data)
  - [Repositioning DOM Elements](#repositioning-dom-elements)
  - [Keyboard Inputs](#keyboard-inputs)
- [Collisions](#collisions)
- [Abstraction Example](#abstraction-example)

# Setup

To install this project, first clone the [template](https://github.com/benspector3/asd-template/) repository by entering these commands into your bash terminal:

```bash
git clone https://github.com/benspector3/asd-template.git
rm -rf asd-template/.git
```

Then, rename the folder to `pong`

# Learning Objectives
- Practice modeling data with Objects
- Reuse code from previous projects to create something new
- Practice abstraction
- Apply the algorithm for detecting collisions between objects

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
- How do those events affect the data of the program?
- For each "event", write out the high-level logic of what will happen. It is better (and tricky) to be as specific as you can while remaining high-level!

For example: in bouncing box, when the box is clicked:
1. The speed is increased
2. The point total goes up by 1 and is displayed on the box
3. The position of the box is reset to 0

# Plan of Attack

The plan for building Pong will be as follows:
1. Create the DOM elements needed for the game with HTML and CSS.
2. Declare variables for data needed for each DOM element. Use objects to group together related data.
3. Create a factory function to reduce repitition when creating game item objects. 
4. Respond to keyboard events by adjusting each paddle's `.velocityY` property.
5. Move the paddles.
6. Move the ball.
7. Identify when the ball collides with the paddles --> Determine how the ball will bounce off.
8. Identify when the ball collides with the top or bottom --> Determine how the ball will bounce off.
9. Identify when a point ends --> Determine what to do to start a new point.
10. End the game when 11 points are reached.

# Helpful Code

Below are some code we've written in the past that may be helpful to you in this project:

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

Later on you may want to add `#score` elements. For now, focus on the ball and the paddles.

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

### Repositioning DOM Elements

We'll need to reposition the ball and each paddle on each update of the timer. Luckily, we've learned how to move things in the past by keeping track of:
- `x` (or `positionX`): the coordinate location of the game item along the x axis
- or `speedX`: the speed (distance over time) and direction (+/-) of the game item along the x axis

And by using the jQuery `.css()` function to draw the element in the new position by changing how far the `$element` is from the `"left"` of the screen: 

```js
$element.css("left", newPositionX)
```

For example, in bouncing box, we have the following function:

```js
function moveBox() {
  positionX = positionX + speedX; // update the position of the box along the x-axis
  $box.css("left", positionX);    // draw the box in the new location, positionX pixels away from the "left"
}
```

If we wanted to move the box vertically, we could also keep track of `positionY` and `speedY` and use the jQuery `.css()` function to change the `"top"` property:

```js
$element.css("top", positionY)
```

### Use Objects to Manage Data

We will need to manage the data for each game item in this project: the ball and each paddle. 

Use objects to manage this data. For example, in bouncing box, we could organize the data for the box like so (shortening `positionX` and `positionY` to `x` and `y`:

```js
var box = {};
box.x = 0;
box.y = 100;
box.speedX = 1;
box.speedY = 0;
box.$element = $("#box");
```

You can then reference the `.$element` property to manipulate the DOM element through jQuery functions like `.css()`. The `moveBox()` function might look like this:

```js
function moveBox() {
  box.x = box.x + box.speedX;       // update the position of the box along the x-axis
  box.$element.css("left", box.x);  // draw the box in the new location, positionX pixels away from the "left"
}
```

Since you'll be creating objects to represent the ball and each paddle, I highly recommend using a factory function to ensure that each `gameItem` has the data below:
- `gameItem.$element`
- `gameItem.x`
- `gameItem.y`
- `gameItem.speedX`
- `gameItem.speedY`

When creating a factory function, the function should return an object that has a specific set of properties already assigned to it. The properties that you want customized for each object should be **parameterized** (turned into parameters/variables).

For example, consider this data for animal objects:

```js
var spot = {};
spot.name = "spot";
spot.species = "dog";
spot.owner = "Farmer Fred";

var daisy = {};
daisy.name = "daisy";
daisy.species = "bird";
spot.owner = "Farmer Fred";

var bessy = {};
bessy.name = "bessy";
bessy.species = "cow";
spot.owner = "Farmer Fred";
```

Since each object shares the same properties; `name`, `species`, and `owner`, I can create a factory function that reduces the repetitive creation of those objects.

For each value that is unique, I will add a parameter to my factory function. Any values that are shared can be hard-coded into the object.

```js
// Initialization
var spot = Animal("spot", "dog");
var daisy = Animal("daisy", "bird");
var bessy = Animal("bessy", "cow");

// Helper Functions
function Animal(name, species) {
  var animal = {};
  animal.name = name;
  animal.species = species;
  animal.owner = "Farmer Fred";
  return animal;
}
```

### Keyboard Inputs

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

# Collisions	

In games, collisions will occur frequently between objects. Having a function that can tell if two objects are colliding would be really convenient! The outline for such a function looks like this:

```js
function doCollide(obj1, obj2) {
  // return false if the objects do not collide
  // return true if the objects do collide
}
```

and we would use such a function in our program like this:

```js
if (doCollide(ball, paddleLeft)) {
  // bounce ball off paddle Left
}
```

Any object passed to our `doCollide` function should store the data for an HTML element. Therefore, they must have an `$element` storing the jQuery object for the HTML element as well as `x` and `y` properties that store where the `$element` is. 

If you haven't set up your object data to represent the ball and the paddles, go back and do so before continuing

For now, let's assume that we have a generic `gameItem` that is passed to the function as one of our objects. It's HTML, CSS, and JavaScript look like this:

```html
<div id="gameItem"></div>
```

```css
#gameItem {
  position: absolute;
  left: 100px;
  top: 50px;
}
```

```js
var gameItem = {};
gameItem.$element = $("#gameItem");
gameItem.x = 100;
gameItem.y = 50;
// speedX and speedY aren't needed for now
```

**To tell if a our game item collides with another, we need to know where the `gameItem.$element`'s boundaries are in space.** This means
1. finding the `y` coordinates of the top and bottom sides (`gameItem.topY` and `gameItem.bottomY`)
2. finding the `x` coordinates of the left and right sides (`gameItem.leftX` and `gameItem.rightX`).

### Left and Top Sides

`gameItem.x` and `gameItem.y` are based on the coordinates of the top-left corner of `gameItem.$element`. So, according to the data above, the **top left corner** of the `gameItem.$element` is 100 pixels from the left of the screen and 50 pixels from the top of the screen. 

This means that we already know that: `gameItem.leftX = gameItem.x` and `gameItem.topY = gameItem.y`.

### Right and Bottom Sides

We can calculate where `gameItem.rightX` is relative to `gameItem.leftX` if we know the **width** of the `gameItem.$element`. Luckily, since `gameItem.$element` is a jQuery object, it comes with a `.width()` method for calculating its own width (as well as a `height()` method)!

Therefore, we can calculate `gameItem.rightX` and `gameItem.bottomY` like so:

```js
gameItem.rightX = gameItem.leftX + gameItem.$element.width();
gameItem.bottomY = gameItem.topY + gameItem.$element.height();
```

If the `gameItem.$element` is a `40px` square, the data might look like this:

```js
var gameItem = {};
gameItem.$element = $("#gameItem");
gameItem.x = 100;
gameItem.y = 50;
gameItem.leftX = 100;   // same as gameItem.x
gameItem.topY = 50;     // same as gameItem.y
gameItem.rightX = 140   // calculated as gameItem.leftX + gameItem.$element.width();
gameItem.bottomY = 90   // calculated as gameItem.topY + gameItem.$element.height();
```

Great! Now that we know how to calculate the four sides of our `gameItem`, we can calculate the four sides of the two objects passed to `doCollide`: `obj1` and `obj2`!

### Collisions, or rather, Non-Collisions
Interestingly, it is far easier to tell if two objects are not colliding than when they are colliding.

The objects are _not colliding_ if:
- The **left side** of one object is to the right of the **right side** of the other. 
- The **top side** of one object is below the **bottom side** of the other. 

Otherwise, they must be colliding.

**Using the data you calculated and the logic above, complete the doCollide function**

```js
function doCollide(obj1, obj2) {
  // calculate obj1.leftX, obj1.rightX, obj1.topY, and obj1.bottomY
  // calculate obj2.leftX, obj2.rightX, obj2.topY, and obj2.bottomY
  
  // IF obj1.leftX is to the left of obj2.rightX:
  //    return false;
  // ELSE IF obj1.topY is below obj2.bottomY:
  //    return false;
  // Repeat with obj2 first and obj1 second
  
  // ELSE:
  //    return true;
}
```

# Abstraction Example	

> Abstraction is the process of turning something specific (hard-coded) into something generic (reusable)	

Repetitive code presents an opportunity to refactor for abstraction. Abstraction helps us to follow the D.R.Y. principle (don't repeat yourself).	

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

