# D3

## My Quest to learn D3.

At my company we are trying to launch an app which takes advantage of D3 to
aid people in better exploring their own ideas.  We determined it was important
to have multiple people who could effectively build charts, so here goes.

First of place I stop is the docs, which prove not very useful at first read.
They do however have this handy section with tutorials:

https://github.com/d3/d3/wiki/Tutorials

I decided on a basic bar graph tutorial and decide to just jump in.

The tutorial is a bit simple but to someone that has only looked at the D3 docs
it introduced some great basics:

*  D3 starts with picking a `selection`, which is just a group of elements.
  Looks something like this `d3.select("body");`.  You're pretty much querying
  a selector if you've used D3. You also slelect a group to elements like
  ``d3.selectAll("body");``

* Method chaining is bigger than sliced bread in D3.
  ```js
  d3.select("body")
  .style("color", "black")
  .style("background-color", "white");
  ```
  The above could go on forever from what I've seen of D3.

Some copy and pasting later I have a basic bar chart:

```js
var data = [4, 8, 15, 16, 23, 42];

d3.select(".chart")
// select the class to target
  .selectAll("div")
  // select the section to join data to (next step)
    .data(data)
    // join the data to the selections choosen
    // each element in the array will be created with a div
  .enter().append("div")
  // This allows for the creation of new data each of which will go in a div
    .style("width", function(d) { return d * 10 + "px"; })
    // Some methods can take functions as arguments do you can get fancy
    // d in this case is an instance of data
    .text(function(d) { return d; });
```

There is something called `.enter(), .update(), and .exit()`

From what I gather thus far when you run `.data(data)`, `.update()` and `.empty()`
are returned.  In this case since there was no already exsiting data `.enter()`
was used to add to the dom. If were wanted to access already existing bound nodes
we would use `.update()`, and to access dead nodes, `.exit()`.

After Doing another bar chart tutorial and encountering a few bugs (note to self
remember `svg class='foo'`). I found a fe more important items to remember when
using D3:

*  Set the height of the svg in javascript so you can base it off the data
  ```js
  var width = 420,
    barHeight = 20;
  ```

Here is what produces a similar bar chart as earlier but in SVG:

```js
var data = [4, 8, 15, 16, 23, 42];

var width = 420,
    barHeight = 20;
    //presetting svg in js

var x = d3.scale.linear()
    .domain([0, d3.max(data)])
    .range([0, width]);

    //set the domain and range, this is the best explaination for this I've
    //found:

    // We take an interval (called Domain by D3.js) and transform it into a new
    // interval (called Range by D3.js).

    //Say you have a big data set and want it to scale in size doman is the big
    //data and range is waht you want it to scale to/

var chart = d3.select(".chart")
    .attr("width", width)
    .attr("height", barHeight * data.length);

    // select the chart and set the chart

var bar = chart.selectAll("g")
// group all the class "g" in d3 'chart' element
    .data(data)
  .enter().append("g")
  // create a new class of 'g' for all elements in 'data'
    .attr("transform", function(d, i) { return "translate(0," + i * barHeight + ")"; });

bar.append("rect")
    .attr("width", x)
    .attr("height", barHeight - 1);
    // appends a rectangle

bar.append("text")
    .attr("x", function(d) { return x(d) - 3; })
    .attr("y", barHeight / 2)
    .attr("dy", ".35em")
    // appends text
    .text(function(d) { return d; });
```
