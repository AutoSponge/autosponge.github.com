---
published: true
layout: post
category: blog
tags: [javascript]
---

Quick example of validating a date on the client-side without regex.

{% highlight js %}
validDate = function (str) {
	var valid = false,
	d  = new Date(str),
	mm = d.getMonth() + 1,
	dd = d.getDate(),
	yyyy = d.getFullYear();

	if (mm < 10) {
		mm = "0".concat(mm);
	}
	if (dd < 10) {
		dd = "0".concat(dd);
	}
	if ([mm, dd, yyyy].join("/") === str) {
		valid = true;
	}
	return valid;
};

/*
validDate("02/29/2010"); //false
validDate("02/29/2012"); //true
validDate("02/29/2012", true); //false
validDate("12/12/2010"); //true
*/
{% endhighlight %}