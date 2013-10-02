---
published: true
layout: post
category: blog
tags: [javascript]
---

jQuery uses a clever bit of code to extract the type of a parameter.  

{% highlight js %}
$.type([]); //array
{% endhighlight %}

You can derive most of JavaScript's weak types using the `typeof` operator.
However, this often returns unexpected results:

{% highlight js %}
typeof new Array(); 	//object -- wrong
typeof []; 		//object -- wrong
typeof 1; 		//number
typeof new Number(1); 	//object -- wrong
typeof NaN; 		//number -- wrong?
typeof null;		//object -- wrong
typeof true; 		//boolean
typeof new Boolean(true); //object -- wrong
typeof "x"; 		//string
typeof new String("x"); //object -- wrong
typeof new Date(); 	//object -- useless
typeof /x/;		//function -- wrong
typeof new RegExp("x");	//function -- wrong
typeof document;        //object -- useless
{% endhighlight %}

jQuery solved this problem by applying the Object.prototype.toString method which returns
more predictable results--albeit in a strange format:

{% highlight js %}
var type = Object.prototype.toString;
type.call(new Array());     //[object Array]
type.call([]);              //[object Array]
type.call(1);               //[object Number]
type.call(new Number(1));   //[object Number]
type.call(NaN);             //[object Number] --rly?
type.call(null);            //[object global] --huh?
type.call(true);            //[object Boolean]
type.call(new Boolean(true)); //[object Boolean]
type.call("x");             //[object String]
type.call(new String("x")); //[object String]
type.call(new Date());      //[object Date]
type.call(/x/);             //[object RegExp]
type.call(new RegExp("x")); //[object RegExp]
type.call(document);        //[object HTMLDocument]
{% endhighlight %}

However, this technique will not work with your home-grown types (Classes).  For instance:

{% highlight js %}
var type = Object.prototype.toString;
var X = function () {};
type.call(new X()); //[object Object]

X.prototype.toString = function () {
    return "[object X]";
};
type.call(new X()); //[object Object]
{% endhighlight %}

You probably expected to see "[object X]".

Well, there's another `Object.prototype` method called `toLocaleString`.
It's best known as a way to localize date objects.  However, as the MDC points out
[here](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Object/toLocaleString),
it returns `toString`, so it can work on any object that has that method.

{% highlight js %}
var type = Object.prototype.toLocaleString;
var X = function () {};
X.prototype.toString = function () {
    return "[object X]";
};
type.call(new X()); //[object X]
{% endhighlight %}

One more hurdle to overcome--just using toLocaleString will cause normal objects to
return their string values instead of a type string:

{% highlight js %}
var type = Object.prototype.toLocaleString;
type(new Array());  // ""
type.call(1);       // 1
type.call("x");     // "x"
type.call(/x/)      // "/x/"
{% endhighlight %}

By using toLocaleString only for "[object Object]" results, we can create a more
convenient type method and get the expected results.  I will also take the time to fix
null, undefined, and NaN.  Using a simple memoizer, I will collect and return the
constructor in lower case like jQuery does:

{% highlight js %}
var type = (function () {
    var memo = {},
    rword = /\w+/g,
    memoize = function (str) {
        if (typeof memo[str] === "undefined") {
            memo[str] = str.match(rword)[1].toLowerCase();
        }
        return memo[str];
    };
    return function (obj) {
        var t, s = Object.prototype.toString,
        ls = Object.prototype.toLocaleString;
        if (typeof obj === "undefined") {
            return "undefined";
        }
        if (obj === null) {
            return "null";
        }
        t = s.call(obj);
        switch (t) {
            case "[object Object]":
                t = ls.call(obj);
                break;
            case "[object Number]":
                if (ls.call(obj) === "NaN") {
                    t = "[object NaN]";
                }
                break;
            default:
        }
        return memoize(t);
    }
}());

var X = function () {};
X.prototype.toString = function () {
    return "[object X]";
};

console.log("type(new Array()) //", type(new Array()), type(new Array()) === "array");
console.log("type([]) //", type([]), type([]) === "array");
console.log("type(1) //", type(1), type(1) === "number");
console.log("type(new Number(1)) //", type(new Number(1)), type(new Number(1)) === "number");
console.log("type(NaN) //", type(NaN), type(NaN) === "nan");
console.log("type(null) //", type(null), type(null) === "null");
console.log("type(true) //", type(true), type(true) === "boolean");
console.log("type(new Boolean(true)) //", type(new Boolean(true)), type(new Boolean(true)) === "boolean");
console.log("type('x') //", type("x"), type("x") === "string");
console.log("type(new String('x') //", type(new String("x")),type(new String("x")) === "string");
console.log("type(new Date()) //",type(new Date()), type(new Date()) === "date");
console.log("type(/x/) //", type(/x/), type(/x/) === "regexp");
console.log("type(new RegExp('x')) //", type(new RegExp('x')), type(new RegExp('x')) === "regexp");
console.log("type(X) //", type(X), type(X) === "function");
console.log("type(new X()) //", type(new X()), type(new X()) === "x");
console.log("type(document) //", type(document), type(document) === "htmldocument");
{% endhighlight %}

EDIT 06-30-2011:
Chrome reports Arguments as "[object Arguments]", which you may think incredibly useful.
However, other browsers return [object Object] and there remains no easy way to differentiate
arguments from non-arguments objects.  So, I recommend adding another case at the end of
the switch statement above:

{% highlight js %}
case "[object Arguments]":
    t = "[object Object]";
    break;

//and add this test:
var a = function () { return arguments; };
console.log("type(a()) //", type(a()), type(a()) === "object");
{% endhighlight %}