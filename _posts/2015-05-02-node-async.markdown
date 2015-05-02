---
layout:     post
title:      "Using Node.js async"
subtitle:   "... with examples"
date:       2015-05-02 13:40:00
author:     "Ashwin"
header-img: "img/nodejs-bg.jpg"
---

<a href="https://www.npmjs.com/package/async" target="_blank">Async</a> is one of the popular contributions to the node.js community. It lets you control the flow of execution in the manner you design a flowchart. Trust me its that simple. 

async's README is PITA. I grew impatient and didn't find the right stuff to get my head around making a node.js script as asynchronous as possible without worrying about maintaining a callbacks and keeping my code clean. 

get async with npm

```
$ npm install async
```

Lets get down to the business with simple console.log examples and I hope it helps if you here to fall in love with async. 

<small><strong>Note:</strong> I’m only going to show two main use of async - Parallel and Series execution. More cool stuff in future post.</small>

Assume you have 3 functions in your node.js javascript that does nothing by console.logs it’s own blah blah! 

{% highlight javascript %} 
function HelloWorldOne(){
    setTimeout(function(){
        console.log("HelloWorld - One");
    }, Math.floor(Math.random() * 1000));
}

function HelloWorldTwo(){
    setTimeout(function(){
        console.log("HelloWorld - Two");
    }, Math.floor(Math.random() * 1000));
}


function HelloWorldThree() {
    setTimeout(function () {
        console.log("HelloWorld - Three");
    }, Math.floor(Math.random() * 1000));
}

HelloWorldOne();
HelloWorldTwo();
HelloWorldThree();

{% endhighlight %}

and your node.js script would output:

{% highlight javascript %} 
HelloWorld - Three
HelloWorld - One
HelloWorld - Two
{% endhighlight %}

and the order changes each time you execute the script. That's the asynchronous behaviour and you have no control to make it execute in one particular order. 

You might always think callbacks is a way and probably end up structuring your code this way:

{% highlight javascript %} 
function HelloWorldOne(callback){
    setTimeout(function(){
        console.log("HelloWorld - One");
        callback(HelloWorldThree);
    }, Math.floor(Math.random() * 1000));
}

function HelloWorldTwo(callback){
    setTimeout(function(){
        console.log("HelloWorld - Two");
        callback();
    }, Math.floor(Math.random() * 1000));
}


function HelloWorldThree(callback) {
    setTimeout(function () {
        console.log("HelloWorld - Three");
    }, Math.floor(Math.random() * 1000));

}

HelloWorldOne(HelloWorldTwo);
{% endhighlight %}


<strong>Sigh! Never program mad!</strong> This looks like a dolphin. 

async makes you have the control you wish to have but also keeps the code clean. 

So let's try again the same example but this time with async:

{% highlight javascript %} 
var async = require("async");

function HelloWorldOne(callback){
    setTimeout(function(){
        console.log("HelloWorld - One");
        callback();
    }, Math.floor(Math.random() * 1000));
}

function HelloWorldTwo(callback){
    setTimeout(function(){
        console.log("HelloWorld - Two");
        callback();
    }, Math.floor(Math.random() * 1000));
}

function HelloWorldThree(callback) {
    setTimeout(function () {
        console.log("HelloWorld - Three");
        callback();
    }, Math.floor(Math.random() * 1000));

}

async.series([HelloWorldOne, HelloWorldTwo, HelloWorldThree], function(){
    console.log("Executed all calls in series.");
})
{% endhighlight %}

and this outputs:

{% highlight javascript %} 
HelloWorld - One
HelloWorld - Two
HelloWorld - Three
Executed all calls in series.
{% endhighlight %}

You get the point now? Simple, isn’t it?

All the asynchronous randomly timed functions HelloWorldOne, HelloWorldTwo, HelloWorldThree started running serially. Appreciate the structure of the code here. 

making use of 
{% highlight javascript %} 
async.series([pass the array of functions calls here], function(){
   do something when all call is executed
});
{% endhighlight %}

and after each function call, call the callback and async handles it like a magic. 

Ok, now let’s try the other way round.. let’s make the synchronous calls run in parallel. 

Consider this same example (without time-outs)

{% highlight javascript %} 
function HelloWorldOne(){
    console.log("HelloWorld - One");
}

function HelloWorldTwo(){
    console.log("HelloWorld - Two");
}

function HelloWorldThree(){
    console.log("HelloWorld - Three");
}

HelloWorldOne();
HelloWorldTwo();
HelloWorldThree();
{% endhighlight %}

outputs:
{% highlight javascript %} 
HelloWorld - One
HelloWorld - Two
HelloWorld - Three
{% endhighlight %}

these calls being independent of each other can be made to run in parallel.

{% highlight javascript %} 
var async = require("async");

function HelloWorldOne(){
    console.log("HelloWorld - One");
}

function HelloWorldTwo(){
    console.log("HelloWorld - Two");
}

function HelloWorldThree(){
    console.log("HelloWorld - Three");
}

async.parallel([HelloWorldOne, HelloWorldTwo, HelloWorldThree], function(){
    console.log("Executed all calls in parallel.");
})
{% endhighlight %}

All the calls run in parallel and the output could be

{% highlight javascript %} 
HelloWorld - Two
HelloWorld - One
HelloWorld - Three
Executed all calls in parallel.
{% endhighlight %}

You see, that's how it's easy to get calls run in parallel and series. 

The good part of this library is you can queue up by combining calls in both series and parallel fashion. 

Assume function HelloWorldOne() should run in series and then HelloWorldTwo() and HelloWorldThree() to run in parallel. 

{% highlight javascript %} 
var async = require("async");

function HelloWorldOne(callback){
    setTimeout(function(){
        console.log("HelloWorld - One");
        callback();
    }, Math.floor(Math.random() * 1000));
}

function HelloWorldTwo(callback){
    setTimeout(function(){
        console.log("HelloWorld - Two");
        callback();
    }, Math.floor(Math.random() * 1000));
}

function HelloWorldThree(callback) {
    setTimeout(function () {
        console.log("HelloWorld - Three");
        callback();
    }, Math.floor(Math.random() * 1000));

}

function parallelTasks(callback){
    async.parallel([HelloWorldTwo, HelloWorldThree], callback);
}

async.series([HelloWorldOne, parallelTasks], function(){
    console.log("Executed calls in both series and parallel fashion!");
})
{% endhighlight %}

and the output will be 
{% highlight javascript %} 
HelloWorld - One
HelloWorld - Three
HelloWorld - Two
Executed calls in both series and parallel fashion!
{% endhighlight %}

Interesting? Yes, definitely it is. Your imagination can go limitless when you have fine control on the execution between synchronous and asynchronous calls. 

Another post on async is coming soon. 

Feel free to mail me at ak@stackexpert.com or tweet me @ashwinkumar_k if you want to have chat about it. 

Cheers, 
<br> 
Ashwin.
