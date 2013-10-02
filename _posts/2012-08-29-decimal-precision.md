---
published: true
layout: post
category: blog
tags: [javascript, performance]
---

While reading the answers to [Nathan Smith's JavaScript Quiz](https://github.com/nathansmith/javascript-quiz),
I was struck by the number of assumptions made around decimal precision.  In two places, the author states "you need to
multiply values by `10`" when in actuality, you need to multiply values by *the precision's power of ten*.

That lead me to write this bit of code to illustrate how you might handle high precision numbers (and of varying precision)
without using a separate library.

{% highlight js %}
function getPrecision(num) {
    var split = (num + "").split(/([\.e\-]{1,2})/);
    var i, len, token, next, nextNum, precision;
    for (precision = 0, i = 1, len = split.length; i < len; i += 1) {
        token = split[i];
        next = split[i + 1];
        nextNum = +next;
        if (token === ".") {
            precision += next.length;
        } else if (token === "e-") {
            precision += i > 1 ? nextNum + 1 : nextNum;
            break;
        }
    }
    return precision;
}

function getMaxPrecision(arr) {
    var i, len, precision, max;
    for (max = 0, i = 0, len = arr.length; i < len; i += 1) {
        precision = getPrecision(arr[i]);
        max = (precision > max) ? precision : max;
    }
    return max;
}

function sumArgs() {
    var args = Array.prototype.slice.call(arguments);
    var max = getMaxPrecision(args);
    var pow = Math.pow(10, max);
    var i, len, sum, arg;
    for (sum = 0, i = 0, len = args.length; i < len; i += 1) {
        arg = args[i];
        sum += isNaN(arg) ? 0 : (arg * pow);
    }
    return sum / pow;
}

console.assert(getPrecision(1) === 0); //no decimal or e-
console.assert(getPrecision(1.1234567890) === 9); //zeros are dropped, just decimal
console.assert(getPrecision(1.1234567890123456) === 16); //longest without rounding
console.assert(getPrecision(0.1) === 1);
console.assert(getPrecision(0.12345678901234567) === 17); //longest without rounding
console.assert(getPrecision(0.0000000001) === 10); //no decimal, just e-
console.assert(getPrecision(0.00000000000000000000000000000000000000000000000000000123456789012345678) === 71);
console.assert(sumArgs(0.00002, 0.00001, 0.1, 0.02) === 0.12003);
console.assert(getPrecision(Math.min()) === 0); //Infinity
console.assert(getPrecision(1e-324) === 0);
console.assert(getPrecision(1e-323) === 323);
{% endhighlight %}

EDIT:  My use of Math.pow made me curious if there was a faster way to build a big number, see the tests:
[http://jsperf.com/make-a-big-number/2](http://jsperf.com/make-a-big-number/2)
