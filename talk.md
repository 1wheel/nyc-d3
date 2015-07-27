# Reusing d3 code with jetpack & starterkit

[1wheel.github.io/nyc-d3](http://1wheel.github.io/nyc-d3/#/)



## Adam Pearce
- [roadtolarissa.com](http://roadtolarissa.com)
- [bloomberg.com/graphics](https://bloomberg.com/graphics)
- [github.com/1wheel](https://github.com/1wheel)
- [@adamrpearce](https://twitter.com/adamrpearce)


## Scatter Plot I
![scatter plot](img/thumbnail.png)

[bl.ocks.org/1wheel](http://bl.ocks.org/1wheel/98129315d0f7df3b53e3)


##Great, but...
- 81 lines & 2284 characters of js

- Reuse conventions with copy/paste


##d3-jetpack
![jetpack comic](https://camo.githubusercontent.com/37eb19461d1dba8d6af9c2a816f488b9bf244691/687474703a2f2f33362e6d656469612e74756d626c722e636f6d2f74756d626c725f6d346b6b7864386e57423172776b7264626f315f3530302e6a7067)

- [github.com/gka/d3-jetpack](https://github.com/gka/d3-jetpack)


##appending with class
Before

````javascript
var legend = svg.selectAll(".legend")
    .data(color.domain())
  .enter().append("g")
    .attr("class", "legend")
````

After

````javascript
var legend = svg.selectAll(".legend")
    .data(color.domain())
  .enter().append("g.legend")
````


##appending with class
Works with ids and multiple classes

Before

````javascript
svg.append("g")
    .attr("class", "y axis")
    .call(yAxis)
  .append("text")
    .attr("class", "label")
````

After

````javascript
svg.append("g.y.axis")
    .call(yAxis)
  .append("text.label")
````


##translate
Before

````javascript
svg.append("g.x.axis")
    .attr("transform", "translate(0," + height + ")")
````

After

````javascript
svg.append("g.x.axis")
    .translate([0, height])
````


##translate
Functions called w/ bound data and index

Before

````javascript
legend
    .attr("transform", function(d, i){
      return "translate(0," + i * 20 + ")" })
````

After

````javascript
legend
    .translate(function(d, i){ return [0, i*20] })
````


##ƒIELD ACCESSOR
Turns a string into a function

````javascript
function ƒ(str){
  return function(object){
    return object[str]
  }
}
````

Returns properties from objects

````javascript
var collie = {color: 'brown', legs: 4}
var spider = {color: 'black', legs: 8}

ƒ('color')(collie)      //'brown'
ƒ('color')(spider)      //'black'

ƒ('legs')(spider)       //8
````


##ƒIELD ACCESSOR

Before

````javascript
x.domain(d3.extent(data, function(d) { return d.sepalWidth; }))
y.domain(d3.extent(data, function(d) { return d.sepalLength; }))
````

After

````javascript
  x.domain(d3.extent(data, ƒ('sepalWidth')))
  y.domain(d3.extent(data, ƒ('sepalLength')))
````


##Compose
Combines two funtions together 

````javascript
function compose(g, h){
  return function(object){
    return h(g(object))
  }
}

function add10 (n){ return 10 + n }
function double(n){ return  2 * n }

compose(ƒ('legs'), add10) (collie)  //4 + 10 = 14
compose(ƒ('legs'), add10) (spider)  //8 + 10 = 18
compose(ƒ('legs'), double)(spider)  //8 * 2  = 16
````


##ƒIELD ACCESSOR

Before

````javascript
dots
    .attr("cx", function(d) { return x(d.sepalWidth); })
    .attr("cy", function(d) { return y(d.sepalLength); })
    .style("fill", function(d) { return color(d.species); })
````

After

````javascript
dots
    .attr("cx", compose(ƒ('sepalWidth'), x))
    .attr("cx", compose(ƒ('sepalLength'), y))
    .style("fill", compose(ƒ('species'), color))
````


##ƒIELD ACCESSOR
Converts strings to functions and composes

````
ƒ('legs', add10) (collie)           //4 + 10 = 14
ƒ('legs'), double)(spider)          //8 * 2  = 16
ƒ(add10, double, add10)(3)          //((3 + 10) * 2 ) + 10 = 36
ƒ()(3)                              //3
````


##ƒIELD ACCESSOR
Before

````javascript
dots
    .attr("cx", compose(ƒ('sepalWidth'), x))
    .attr("cx", compose(ƒ('sepalLength'), y))
    .style("fill", compose(ƒ('species'), color))
````

After

````javascript
dots
    .attr("cx", ƒ('sepalWidth', x))
    .attr("cx", ƒ('sepalLength', y))
    .style("fill", ƒ('species', color))
````


##ƒIELD ACCESSOR
Also works as an identity function

Before

````javascript
legend.append("text")
    .text(function(d) { return d })
````

After

````javascript
legend.append("text")
    .text(ƒ())
````


##d3-starterkit
Snippets and conventions for starting a new d3 project

[github.com/1wheel/d3-starterkit](https://github.com/1wheel/d3-starterkit)


##dataAppend
Before

````javascript
svg.selectAll(".dot")
    .data(data)
  .enter().append("circle.dot")
````

After

````javascript
svg.dataAppend(data, "circle.dot")
````


##dataAppend
````javascript
d3.selection.prototype.dataAppend = function(data, name){
  return this.selectAll(name)
      .data(data).enter()
    .append(name)
````


##dataAppend
Before

````javascript
var legend = svg.selectAll(".legend")
    .data(color.domain())
  .enter().append("g")
````

After

````javascript
legend = svg.dataAppend(color.domain(), "g.legend")
````



## Outline 
- `append('el.class`): axis, circle, legend
- `translate`: x axis, legend
- `ƒ`: scales, identity for text, compose attrs
- `appendData`: circle, legend
- `conventions`: margin, scale, axis


## Links
[jetpack](https://github.com/gka/d3-jetpack)

[scatter plot](http://bl.ocks.org/mbostock/3887118)

[data munster](https://github.com/sarahgp/data-monster)

[d3kit](https://github.com/twitter/d3kit)

[rollup](https://github.com/rollup/rollup)

[come work with us!!!](http://jobs.bloomberg.com/job/New-York-Interactive-Graphic-Journalist-Job-NY/276897600/)
