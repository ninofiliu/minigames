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
`y(t)=y(0)+yv(0)*t-(g/2)*t²` where g is the gravitational constant and yv is the object's vertical velocity, which means `y'(t)=yv(t)`.



  















