# Intro to D3

## First SVGs

What is an SVG?

* A scaleable vector graph.

Why a SVG?

* It allows images to scale without being grainy like JPGs or other
image files types.

* You can draw and control your charts and graphs with code.

Let's create an SVGs:

```html
<svg width="50" height="50">
  <circle cx="25" cy="25" r="25" fill="purple" />
</svg>
```

* The `SVG` tag sets the the height and width for the SVG canvas.
* The `circle` tag which goes inside the SVG canvas takes certain attributes in
  order to draw a circle. For circles and elipse, `cx` is the centerpoint of
  the x-axis, `cy` is the same for the y-axis. `r` is the radius of the circle.

Note: The coordinate plane is not what you think. I'll cover it more later.
For now just use trial and error.

## Lab

For a fun lab try to create 3 interconnected rings, like a snowman.

## D3

Now that we can draw a circle in HTML lets try to do it using D3.

```js
d3.select("body").append("svg")
  .attr("width", 50)
  .attr("height", 50)
.append("circle")
  .attr("cx", 25)
  .attr("cy", 25)
  .attr("r", 25)
  .style("fill", "purple");
```

This clearly draws one circle but how it does so should be easy to see.

1. Selects and appends and `SVG` to the `body`.
2. Sets its attributes.
3. Appends a circle to it.
4. Sets it's attributes and styles.

Say we wanted to further break that up into some more modular and easily
replicatable:

```js
let bodySelection = d3.select("body");

let svgSelection = bodySelection.append("svg")
      .attr("width", 50)
      .attr("height", 50);

let circleSelection = svgSelection.append("circle")
      .attr("cx", 25)
      .attr("cy", 25)
      .attr("r", 25)
      .style("fill", "purple");
```

Now if we wanted to isolate the creation of the circle we have it stored in a
letiable.  This is a common D3 pattern.

## Binding Data to the Dom

Below is a simplistic example of D3 data binding.

```js
let data = [ 1, 2, 3]

let p = d3.select("body").selectAll("p")
  .data(data)
  .enter()
  .append("p")
  .text("hello world");
```

Let's break this down:

```js
let p = d3.select("body").selectAll("p")
```

Using `d3.select('foo')` we grab the body and with `.selectAll("p")` we grab all
the elements with a `p` tag.  Returned will be a selection of all the `p` tags.
If there are no `p` tags and empty selection will be returned. *This is*
*important because it lets you chain methods.*

```js
.data(data)
```

The `.data()` method binds an array to the selection element by element.  In
this case it binds `[1,2,3]` (stored as the `data` variable) to the selection
of `p` tags we grabbed in the line above. One per `p` tag.

_But the `p` tags may not exist, what's happening?_

To understand the answer to that question you have to understand what the `.data()`
method returns and what `virtuals` are.

`.data()` returns _3_ virtuals `.enter()`, `.update()` and `.exit()`.

Think of these like 3 *potential* trees.

* The `.enter()` tree is for new additions. Returned is a reference to the
  placeholder `p` tags that do not already have a corresponding DOM element. Think
  of this as adding new data in the form of what you previous selected.
  - *only allows use of `.append()`, `.insert()`, and `.select()` directly on it.*

* `.update()` is for updating in a similar manner as `.enter()`.  More on this
  later.

*  `.exit()` we'll cover later.

```js
.append("p")
.text("hello world");
```

This last bit just appends as many `p` tags as there are elements, and sets the
text of each to `hello world`.

_But where did the data go?_

If you enter `console.log(d3.select("body").data([1]));` and drop down a few
you should see a `__data__` property. That is where the data is stored.

This is done so it is `sticky` but don't worry about it for now.

## Data Retrival

So we managed to put data into the DOM, now how to get it out.

Let's take the code from earlier and change just that `.text()` method.

```js
let data = [ 1, 2, 3]

let p = d3.select("body").selectAll("p")
  .data(data)
  .enter()
  .append("p")
  .text( function (d) { return d; } );
```

This `.text()` method, when passed a function each element is evualated in order
and in this case sets each elements text. (The `d` here is the data).

# D3 Availale Variables: d, this, and i

As seen in earlier examples `d` inside the `.text()` method reffered to the data.
`d` is not exclusive to D3's `.text()` method.

`this`, as you can imagine refers to the current DOM element.

`i`, much like in a standard for loop, refers to the current index of the element.

If you want and example try running this code:

```js
const theData = [ 1, 2, 3 ]

let p = d3.select("body").selectAll("p")
  .data(theData).enter()
    .append("p")
    .text(function (d,i) {
      return `i = ${i} d = ${d}`
    });
```

# Lab: Building a Bullseye

Create a bullseye target using the provided dataset. Change the size and color
of each ring depending on the data provided.

`const circleData = [40, 20, 10]`

Answer Below:

```js
//  let svg = d3.select("body").append("svg")
//                                      .attr("width", 600)
//                                      .attr("height", 100);
//
//  let circles = svg.selectAll("circle")
//                            .data(circleData)
//                            .enter()
//                           .append("circle")
//
// let circleAttributes = circles
//                        .attr("cx", 50)
//                        .attr("cy", 50)
//                        .attr("r", function (d) { return d; })
//                        .style("fill", function(d) {
//                          var returnColor;
//                          if (d === 40) { returnColor = "green";
//                          } else if (d === 20) { returnColor = "purple";
//                          } else if (d === 10) { returnColor = "red"; }
//                          return returnColor;
//                        });
```

## Positioning in 2D Space

SVG Graphs _DO NOT_ share the same coordinate space you are used to.

If you were to devide the traditional graph coordinate space into four quadrents
for variations of positive and negative x and y values, the D3 portion would be
the bottom right quadrent.  Please see the image below.

![svg_coordinate_graph_circle_drawing_331x200](https://cloud.githubusercontent.com/assets/10408784/21021961/daac1dcc-bd48-11e6-9792-2dc96d20c64c.png)

This graph represents x=30 and y=30.

## Lab: Dynamically Positioning Circles in 2D space

Say we want to position 3 circles in 2D space using provided data.  We already
know we can pass functions into the `.text()` method. Try doing the same,
adjusting the the `cy` and `cx` attributes to be based off of the following
data set.

const circleData = [40, 20, 10]

# More D3
## A Note on data

D3's `.data()` method takes an array.  So it is important to make sure your data
is formatted properly.  You can pass in a plain array, an array of objects, an
array or arrays, etc.

## More Data Binding.

Say we have an API which returns data and we want to use D3 create some
visulations with it.  First off our data would have to an array.

```js
let json = [
 { "name": "Joe" "x_axis": 30, "y_axis": 30, "radius": 20, "color" : "green"},
 { "name": "Bob" "x_axis": 70, "y_axis": 70, "radius": 20, "color" : "purple"},
 { "name": "Jane" "x_axis": 110, "y_axis": 100, "radius": 20, "color" : "red"}];
```

If we were to bind this data in the standard way we have done thus far:

```js
d3.select("body").append("svg").attr("width", 200).attr("height", 200).selectAll("circle").data(json).enter().append("circle");
```

and inspect one of the elements (try `console.log`ing the above code).
and look at the `__data__` attribute we discussed earlier we would see all of
our JSON stored there.

If we wanted to access any of the data we could just do something like

```js
.attr("r", function (d) { return d.radius; })
```

## D3 Paths
