---
published: false
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

`
var arr = Array(9);
var str = ""; 
arr.forEach(function () {
    str += "\<td\>\<\/td\>";
});
 
//another attempt
arr.map(function () {
    return "\<td\>\<\/td\>";
});
`