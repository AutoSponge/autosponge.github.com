---
published: true
layout: post
category: blog
tags: javascript
---

A sparse array is an instance of `Array` that has `undefined` as one or more members. I’ve seen them used, for performance reasons, in game engines to pre-allocate a buffer. I believe it has something to do with one versus two operations–altering a member versus adding a member and increasing length. But there are other uses for them if you know what you can and can’t do with them.

Ways to create sparse arrays:

1. `Array(n)` where n is an integer, will create an array with n `undefined` members
2. Alter the length property of an existing array (with fewer members than the new length): `var arr = []; arr.length = 1;`
3. Delete a member from an existing array, or by deleting a member. `var arr = [1,2,3]; delete arr[1];`
4. Set a member of an existing array to `undefined`. `var arr = [1,2,3]; arr[1] = void(0);`
5. Create an array literal with commas and no value: `var arr = [,,,];`

A co-worker was trying to make a repeating string and used a sparse array; it looked something like this:

{% highlight javascript %}
var arr = Array(9);
var str = ""; 
arr.forEach(function () {
    str += "\<td\>\<\/td\>";
});
 
//another attempt
arr.map(function () {
    return "\<td\>\<\/td\>";
});
{% endhighlight %}

Both statements fail for the same reason: the JavaScript engine does not iterate over `undefined` members of an array. Iteration based on length, like typical `for` and `while` loops, work fine. But `.forEach` and `.map` work like for-in which skips the `undefined` member(s).

{% highlight javascript %}
//setup
var arr = [];
arr.length = 10;
arr[5] = "";
var count = 0;
 
//test for
var i, len;
for (i = 0, len = arr.length; i < len; i += 1) {
    count += 1;
}
console.log(count); //10
 
//test while
count = 0;
var i = arr.length;
while (i--) {
    count += 1;
}
console.log(count); //10
 
//test for-in
var i, count = 0;
for (i in arr) {
    count += 1;
}
console.log(count); //1
 
//test forEach
var count = 0;
arr.forEach(function () {
    count += 1;
});
console.log(count); //1
 
//test map
var count = 0;
arr.map(function () {
    count += 1;
});
console.log(count); //1
{% endhighlight %}

I came up with some variations of this idea that work (seen here: [jsPerf](http://jsperf.com/build-mostly-empty-tds)). The test task was to write code that will inject a “test” td at the fourth position of 9 (otherwise empty) td’s. Some are quite nice syntactically, but none seem to perform like a `for` loop. This one was my favorite for its simplicity.

{% highlight javascript %}
Array.prototype.join.call({3: "<td>test</td>", length: 9}, "<td></td>");
{% endhighlight %}