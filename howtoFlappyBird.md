-------not finished yet-------
# How to code Flappy Bird
*by Nino Filiu*

In the following document I'll explain what is for me the easiest way to code a minimalist version of the famous game Flappy Bird. I'll implement this game on an HTML page using JavaScript. This tutorial is targetted at those who are beginning in JavaScript and requires only the understanding of basic HTML and JavaScript.

## The basics

For me, the easiest way to implement a game is to manipulate an HTML canvas with JavaScript because there are a lot of already-implemented functions useful for game-oriented coding, it is also quite fast. Thus, the HTML page will only consist of one 400px by 400px canvas with the JavaScript code below:

#### FlappyBird.html
```html
<canvas width="400" height="400"></canvas>
<script>
  // JS will go here
</script>
```

On the page load, we'll get JS to initiate the game. For this to happen, we'll:

- fetch the canvas' context (basically the object where everything that happens on the canvas is coded).
- We'll also ask the page to "listens" when the client pushes a key: indeed, everytime the user presses the space bar, the bird must jump.
- Finally we'll make the document call the "game" function 40 times every seconds. That's basically a way to refresh the screen every 40 seconds while taking account of how the game is supposed to works.

The HTML code now looks like this:

#### FlappyBird.html
```html
<canvas id="c" width="400" height="400"></canvas>
<script>
  window.onload=function(){
    ctx=document.getElementById("c").getContext("2d");
    document.addEventListener("keydown",keypush);
    setInterval(game,1000/40);
  }
  function keypush(evt){
    // a function called everytime the user presses a key
    // evt is the keyboard event of the key pushing, which is basically an object where its fields gives infos about the key that the user just pressed
  }
  function game(){
    // a function called 40 times a seconds
  }
</script>
```

The basic task of *game* is described above. It is supposed to refresh the screen everytime it is called. In this regard, we'll have a *display* function that gets called everytime *game* ends:

#### FlappyBird.html
```html
<canvas id="c" width="400" height="400"></canvas>
<script>
  window.onload=function(){
    ctx=document.getElementById("c").getContext("2d");
    document.addEventListener("keydown",keypush);
    setInterval(game,1000/40);
  }
  function keypush(evt){
    //
  }
  function game(){
    display();
  }
  function display(){
    //
  }
</script>
```

Now we have the basic implementation of Flappy Bird. Time to look at how we can code the bird and the obstacles.

## The objects

JavaScript is an object-oriented language. That basically means that variable can and are supposed to execute the value changes that are on the domain of their fields: notice for example theses differences in syntax between PHP and JS:

```
// get the length of an array
count($arr); <-- in PHP, the script itself calculates the length of the array
arr.length; <-- in JS, the array gets the length by itself
// add an element at the end of an array
$arr[]=$elt; <-- in PHP, the script itself adds an element at the end of an array
arr.push(elt); <-- in JS, the array adds the element by itself
```

There is a lot to say about object-oriented programmation but we'll not go into details here. Basically all you need to know is that objects in JS have fields and methods. Fields is where the informations about the object are stocked, methods are functions. for arrays, *length* is a field and *push* is a function.

Given this short introduction to objects, you're now supposed to understand the following indications on how we're gonna code the game:
- The bird is going to be an object
- The bird is going to have fields giving its position
- The bird is going to have methods giving him the capacity to jump&fly
- Obstacles are going to be objects
- Obstacles are going to have fields giving their position

To define an object, the syntax can be understood by the following example:

```javascript
function myObject(var1,var2){
  this.field1=var1;
  this.field2=var1+var2;
  this.method1=function(){
    a=5;
    this.field1++;
    return(this.field2/a);
  }
}
```

Given the instructions about how we're going to implement the bird and the obstacles, the JavaScript code of the html document looks like this:

```javascript
function bird(x,y){
  this.x=x;
  this.y=y;
  this.yv=0; // I'll explain this later
  this.move=function(){
    // we'll code it later
  }
}
function obstacle(x,y){
  this.x=x;
  this.y=y;
}
window.onload=function(){
  ctx=document.getElementById("c").getContext("2d");
  document.addEventListener("keydown",keypush);
  setInterval(game,1000/40);
}
function keypush(evt){
  //
}
function game(){
  display();
}
function display(){
  //
}
```

Now, we're going to initialise the game with one bird at (100,200) and an empty list of obstacles by changing the *onload* function:

```javascript
window.onload=function(){
  ctx=document.getElementById("c").getContext("2d");
  document.addEventListener("keydown",keypush);
  setInterval(game,1000/40);
  
  b=new bird(100,200); // bird
  o=[]; // obstacles
}
```

We're done with creating the objects of the game. Now, let's code the bird's movement.

## Bird's movement

Quick physics lesson: when an object falls without friction, its altitude is a parabola over time:
`y(t)=y(0)+yv(0)*t-(g/2)*t²` where g is the gravitational constant and yv is the object's vertical velocity, which means `y'(t)=yv(t)=-gt`. If we capture the movement at regular intervals we have `y((n+1)T)=y(nT)+(approx)T*yv(nT)` and `yv((n+1)T)=yv(nT)-gT`. We're going to use these two equations to code the movement of the bird.

Basically, what this means in our code is that a good and simple way to mimic the effect of gravity on the bird is to code the following:
*Don't forget that on a computer screen, the y axis goes downward*

```javascript
function bird(x,y){
  this.x=x;
  this.y=y;
  this.yv=0; // the bird is created with a y-velocity equal to zero
  this.move=function(){
    this.y+=this.yv; // the bird's altitude is refreshed according to the first equation
    this.yv+=NUMBER; // the bird's verrtical velocity is refreshed according to the second equation
                     // The bigger NUMBER is, the harder the gravity will be
                     // NUMBER=1 is fine so this line can be replaced by this.yv++;
  }
}
```

Now we have a bird that is free falling if we call *b.move()* regularly. Let's just modify a bit the code so that the bird can't fly above y=0 and below y=400. One reminder and one info to understand the following modification: computer's y-axis goes downward, and the bird will appear on the screen as a square 20-pixels-wide square centered on the the bird's position. This last info means that the bird appears "on the floor" if *this.y=390* and "touching the ceiling" if *this.y=10*.

```javascript
function player(y){
  this.x=100;
  this.y=y;
  this.yv=0;
  this.move=function(){
    fy=this.y+this.yv; // fy = future y
    if (fy<10){
      this.y=10;
      this.yv=0;
    }
    else {
      if (fy>=390){
        this.y=390;
        this.yv=0;
      }
      else {
        this.y=fy;
        this.yv++;
      }
    }
  }
}
```

Finally, to make the bird do a free fall, we must call *b.move()* regularly, which we'll do in *game*:

```javascript
function game(){
  b.move();
  display();
}
```

The bird is now free falling. We want that whenever the client presses the spacebar, the bird jump. A bird jumping means that its vertical velocity takes physically a vertical value (so on the script, a negative value). We'll implement this on the *keypush* function that is called everytime the client presses a key:

```javascript
function keypush(evt){
  if (evt.keyCode==32){ // = if the user have pressed the spacebar
    b.yv=-NUMBER; // the greater the number, the bigger the jump. 10 is fine here.
  }
} 
```

We now have a bird that is completely controllable by the player and who moves as if it was under the subject of gravity. Before coding the obstacles, here's how the code looks like:

```javascript
function player(y){
  this.x=100;
  this.y=y;
  this.yv=0;
  this.move=function(){
    fy=this.y+this.yv;
    if (fy<10){
      this.y=10;
      this.yv=0;
    }
    else {
      if (fy>=390){
        this.y=390;
        this.yv=0;
      }
      else {
        this.y=fy;
        this.yv++;
      }
    }
  }
}
function obstacle(x,y){
  this.x=x;
  this.y=y;
}
window.onload=function(){
  ctx=document.getElementById("c").getContext("2d");
  document.addEventListener("keydown",keypush);
  setInterval(game,1000/40);
  b=new bird(100,200);
  o=[];
}
function keypush(evt){
  if (evt.keyCode==32){
    b.yv=-10;
  }
}
function game(){
  b.move();
  display();
}
function display(){
  //
}
```

## Obstacles

An obstacle is basically two rectangles emerging from the top and the bottom of the screen. They are defined only by a position (x,y) that is basically the center of the "whole" between the two rectangles. First, we need to regularly create them. For this, we'll count how many times we have already called *game* and based on this information, we'll create a new obstacle every, let's say, every 45 calls to game:

```javascript
window.onload=function(){
  ctx=document.getElementById("c").getContext("2d");
  document.addEventListener("keydown",keypush);
  setInterval(game,1000/40);
  b=new bird(100,200);
  o=[];
  step=0; // number of times that we have called game()
}
function game(){
  step++;
  if (step%45==0){
    // the rectangles will be 40-pixels wide and the whole between will be 110-pixels tall
    x=420; // we'll create the obstacle at the right of the canvas
           // 420 because 400 (width of canvas) + 20 (semi-width of obstacle)
    y=55+Math.floor(290*Math.random()); // random number between 55 and 345
    o.push(new obstacle(x,y)); // we add the obstacle with these coordinates to the obstacles list
  }
  b.move();
  display();
}
```

Now we'll have to make them move to the left, which is basically just regularly subtracting the same value to their x-coordinates:

```javascript
function game(){
  step++;
  if (step%45==0){
    x=420;
    y=55+Math.floor(290*Math.random());
    o.push(new obstacle(x,y));
  }
  for (i=0; i<o.length; i++){
    o[i].x-=NUMBER; // the greater the number, the greater the speed at which the obstacles move. 4 is fine.
  }
  b.move();
  display();
}
```

Now we have a moving obstacles and a moving bird. Before we go on to the actual "game" coding (collisions, game over triggering, etc), here's how the code should look like:

```javascript
function player(y){
  this.x=100;
  this.y=y;
  this.yv=0;
  this.move=function(){
    fy=this.y+this.yv;
    if (fy<10){
      this.y=10;
      this.yv=0;
    }
    else {
      if (fy>=390){
        this.y=390;
        this.yv=0;
      }
      else {
        this.y=fy;
        this.yv++;
      }
    }
  }
}
function obstacle(x,y){
  this.x=x;
  this.y=y;
}
window.onload=function(){
  ctx=document.getElementById("c").getContext("2d");
  document.addEventListener("keydown",keypush);
  setInterval(game,1000/40);
  b=new bird(100,200);
  o=[];
  step=0;
}
function keypush(evt){
  if (evt.keyCode==32){
    b.yv=-10;
  }
}
function game(){
  step++;
  if (step%45==0){
    x=420;
    y=55+Math.floor(290*Math.random());
    o.push(new obstacle(x,y));
  }
  for (i=0; i<o.length; i++){
    o[i].x-=4;
  }
  b.move();
  display();
}
function display(){
  //
}
```

## Actual game implementation

First, let's implement the "game over". A minimalist way to do it is to initalise a *gameover* variable to *false* and execute something in the *game* function only if the game is not over (i.e. *gameover* is equal to *false*):

```javascript
window.onload=function(){
  ctx=document.getElementById("c").getContext("2d");
  document.addEventListener("keydown",keypush);
  setInterval(game,1000/40);
  b=new bird(100,200);
  o=[];
  step=0;
  gameover=false;
}
function game(){
  if (!gameover){
    step++;
    if (step%45==0){
      x=420;
      y=55+Math.floor(290*Math.random());
      o.push(new obstacle(x,y));
    }
    for (i=0; i<o.length; i++){
      o[i].x-=4;
    }
    b.move();
    display();
  }
}
```

Now let's see when to trigger the end of the game. In this version, let's say that the only way to lose the game is to make the bird touch one of the obstacle. A reminder that the bird will appear as 20x20 square and the obstacle's "whole" will appear as a 40x110 rectangle. If the bird have for coordinates (bx,by) and an obstacle (ox,oy), they touch if `|bx-ox|<30` and `|by-oy|>45`. Trust me, or check it for yourself with a quick sketch. A collision detection can be thus implemented like this:

```javascript
function game(){
  if (!gameover){
    step++;
    // obstacles creation
    if (step%45==0){
      x=420;
      y=55+Math.floor(290*Math.random());
      o.push(new obstacle(x,y));
    }
    // obstacles & bird moving
    for (i=0; i<o.length; i++){
      o[i].x-=4;
    }
    b.move();
    
    // collision detection
    for (i=0; i<o.length; i++){
      if (Math.abs(p.x-o[i].x)<30){
        if (Math.abs(p.y-o[i].y)>45){
          gameover=true;
        }
      }
    }
    
    // display
    display();
  }
}
```

Great, now we have fully functionning game! The last thing to implement is the display. Before we move there, here's how the code should look like:

```javascript
function player(y){
  this.x=100;
  this.y=y;
  this.yv=0;
  this.move=function(){
    fy=this.y+this.yv;
    if (fy<10){
      this.y=10;
      this.yv=0;
    }
    else {
      if (fy>=390){
        this.y=390;
        this.yv=0;
      }
      else {
        this.y=fy;
        this.yv++;
      }
    }
  }
}
function obstacle(x,y){
  this.x=x;
  this.y=y;
}
window.onload=function(){
  ctx=document.getElementById("c").getContext("2d");
  document.addEventListener("keydown",keypush);
  setInterval(game,1000/40);
  b=new bird(100,200);
  o=[];
  step=0;
  gameover=false;
}
function keypush(evt){
  if (evt.keyCode==32){
    b.yv=-10;
  }
}
function game(){
  if (!gameover){
    step++;
    // obstacles creation
    if (step%45==0){
      x=420;
      y=55+Math.floor(290*Math.random());
      o.push(new obstacle(x,y));
    }
    // obstacles & bird moving
    for (i=0; i<o.length; i++){
      o[i].x-=4;
    }
    b.move();
    
    // collision detection
    for (i=0; i<o.length; i++){
      if (Math.abs(p.x-o[i].x)<30){
        if (Math.abs(p.y-o[i].y)>45){
          gameover=true;
        }
      }
    }
    
    // display
    display();
  }
}
function display(){
  //
}
```

## Display

This is the easiest part once you've understood how to display thing on a canvas. Basically in the canvas' context, fillStyle is the field of the color in which we are drawing, and fillRect(x,y,width,height) is the method to fill draw a rectangle of the color fillStyle.

Given these information, *display* is just the following function:

```javascript
function display(){

  // fill the whole canvas black to get rid of the previous display
  ctx.fillStyle="black";
  ctx.fillRect(0,0,400,400);
  
  // draw all the obstacles
  ctx.fillStyle="white";
  for (i=0; i<o.length; i++){
    ctx.fillRect(o[i].x-20,0,40,o[i].y-55);
    ctx.fillRect(o[i].x-20,o[i].y+55,40,400-o[i].y+55);
  }
  
  // draw the bird
  ctx.fillStyle="red";
  ctx.fillRect(b.x-10,b.y-10,20,20);
}
```

## Aaaaaand there you have it.

```html
<canvas width="400" height="400"></canvas>
<script>
function player(y){
  this.x=100;
  this.y=y;
  this.yv=0;
  this.move=function(){
    fy=this.y+this.yv;
    if (fy<10){
      this.y=10;
      this.yv=0;
    }
    else {
      if (fy>=390){
        this.y=390;
        this.yv=0;
      }
      else {
        this.y=fy;
        this.yv++;
      }
    }
  }
}
function obstacle(x,y){
  this.x=x;
  this.y=y;
}
window.onload=function(){
  ctx=document.getElementById("c").getContext("2d");
  document.addEventListener("keydown",keypush);
  setInterval(game,1000/40);
  b=new bird(100,200);
  o=[];
  step=0;
  gameover=false;
}
function keypush(evt){
  if (evt.keyCode==32){
    b.yv=-10;
  }
}
function game(){
  if (!gameover){
    step++;
    // obstacles creation
    if (step%45==0){
      x=420;
      y=55+Math.floor(290*Math.random());
      o.push(new obstacle(x,y));
    }
    // obstacles & bird moving
    for (i=0; i<o.length; i++){
      o[i].x-=4;
    }
    b.move();
    
    // collision detection
    for (i=0; i<o.length; i++){
      if (Math.abs(p.x-o[i].x)<30){
        if (Math.abs(p.y-o[i].y)>45){
          gameover=true;
        }
      }
    }
    
    // display
    display();
  }
}
function display(){
  ctx.fillStyle="black";
  ctx.fillRect(0,0,400,400);
  ctx.fillStyle="white";
  for (i=0; i<o.length; i++){
    ctx.fillRect(o[i].x-20,0,40,o[i].y-55);
    ctx.fillRect(o[i].x-20,o[i].y+55,40,400-o[i].y+55);
  }
  ctx.fillStyle="red";
  ctx.fillRect(b.x-10,b.y-10,20,20);
}
</script>
```

You now have a fully functionning minimalist game of Flappy Bird in 370 lines. This is sure not perfectly coded and there are big performance flaws that can be easily corrected, but still: it works! Enjoy the game by pasting the html code in an html file and opening it using your favorite web browser.

I hope you have enjoyed this tutorial and that this have helped you learn something about JavaScript, object-oriented programming and arcade game programming.

**by Nino Filiu - ninofiliu.fr/games**
