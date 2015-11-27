---
layout:     post
title:      I hate making graphs
date:       2015-11-27 11:21:29
summary:    Making a real-time graph is hard, especially with d3.
categories: JavaScript react chart d3 c3
---

> About 6 month ago I tried to find a simple real-time chart for my JavaScript application. To be more precise: I needed a simple library that allowed me to easily create a real-time time series graph with React.

So I asked Google, but the result was a bit ```depressing```. Most of the search results was different ways of using [d3.js](http://d3js.org/), for example this [article by Mike Boston](http://bost.ocks.org/mike/path/). Which is fine, if you are planning of spending all your weekends scratching your head with vectors, paths and linear algebra. I don't have time for that stuff. I have a kid!

### Well, ```I did``` anyway
After some scratching I finished my real-time chart and the result was good enough for my use case.

Then I figured the end result was not necessarily what I was searching for. The graph looked OK but the implementation was too difficult to work with.

> If you really don't need/want to go the complicated path using d3, try an easier path first. If that work out, you don't need to ```scratch you head``` that much.

### Why it didn't work out

The main reason: it was too hard to maintain. If you look at the code below you might understand why:

```javascript
  .transition()
    .duration(700)
    .ease("ease-in")
    .attr("transform",
    "translate("+
        -(x(data[data.length-1].date) - x(data[data.length-2].date))+",0)")
  ...

  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
  .append("g")
```

I know. It’s not pretty. I got about **150** lines of code that looked like this. The graph was ugly too!

Event dough I made some nice abstraction, I still needed most of the lines!
When the chart need some adjustment I don't won’t to open the file and browse 150 lines of linear algebra.

#### Which made me hate making graphs

For these simple reasons:

1. I don't want to make graphs
2. I am not good at making graphs
3. I don't like linear algebra
3. I just want to display my data

So I looked for an easier option that could fit my ```not-adapted-linear-algebra-graph brain```.

There are many chart libraries for JavaScript out there. Most of them are based on either canvas or d3.

Some examples:

* Google charts
* ChartJS
* Highchart/Highstock
* Epoch
* And so on..

But I could not find any library that gave me a simple real-time chart. Those I found were either ```using jquery, not open source, not svg or did not have the right abstraction```.

#### Then I discovered c3.js

[c3.js](http://c3js.org/) is wrapper on top of d3. It’s open source, widely used, easy to configure and give you a bunch of different graph components. One of the options is the flow API, that lets you create ```real-time``` charts on top of d3 (Which I was looking for).

c3 was easy to use, customizable, and the charts looked nice.
The code was highly maintainable as well:

```javascript
var chart = c3.generate({
    data: {
        x: 'x',
        columns: [
            ['x', '2012-12-29', '2012-12-30', '2012-12-31'],
            ['data1', 230, 300, 330],
            ['data2', 190, 230, 200],
            ['data3', 90, 130, 180],
        ]
    },
  ...
```
Before the graph code was completely welded into my application I figured it was a good idea to make a simple React component that others with ```not-adapted-linear-algebra-graph brains``` could use.

### React component
I ended up making a react component called react-rt-chart.

The implementation of the component/library was highly inspired by my own needs:

- Load initial data into the graph
- Update the graph with new values
- Possible to adjust the maximum number of values in the graph

#### The component

Using c3 was painless, and creating a simple generic react component went pretty fast.

Using the component is fairly easy and the example below should be pretty clear:

```javascript
componentDidMount() {
    setInterval(() => this.forceUpdate(), 1000);
},

render() {
    var data = {
      date: new Date(),
      Car: getRandomValue(),
      Bus: getRandomValue()
    };

    return <RTChart
            fields={['Car','Bus']}
            data={data} />
}
```

![Awesome cat gif](http://imgur.com/BgABXwt.gif)

The component is available on [npm](https://www.npmjs.com/package/react-rt-chart) and [github](https://github.com/emilmork/react-rt-chart).

### You should scratch your head!

I made a short cost benefit analysis, where a combination of laziness, lack of time and interest made me take an easier approach this time.

But, I would still encourage anyone to learn d3. It’s a powerful weapon that can be used to almost anything
For example, [visualize Obamas budget proposal]( http://www.nytimes.com/interactive/2012/02/13/us/politics/2013-budget-proposal-graphic.html?_r=0).
