---
published: true
layout: post
category: blog
tags: [javascript, es6, web components, polymer]
---

ES6 brings a lot of new functionality to objects.  You can get a 
[nice walk-through on Bjorn Tipling's site](http://bjorn.tipling.com/advanced-objects-in-javascript). 

The basic object, created with no prototype, causes problems for Polymer.

{% highlight html %}
<link rel="import" href="../polymer/polymer.html">
<polymer-element name="es6-object" attributes="name">
  <template>
    Hello {{ "{{foo "}}}}
  </template>
  <script>
    (function () {
      var config = Object.create( null, {
        foo: {
          value: 'bar'
        }
      } );
      Polymer( 'es6-object', config );
    }());
  </script>
</polymer-element>
<!-- Hello -->
{% endhighlight %}

So, we need at least the basic Object prototype. 

{% highlight html %}
<link rel="import" href="../polymer/polymer.html">
<polymer-element name="es6-object" attributes="name">
  <template>
    Hello {{ "{{foo "}}}}
  </template>
  <script>
    (function () {
      var config = Object.create( {}, {
        foo: {
          value: 'bar'
        }
      } );
      Polymer( 'es6-object', config );
    }());
  </script>
</polymer-element>
<!-- Hello bar -->
{% endhighlight %}

But adding additional properties to the prototype won't transfer to the web component. 

{% highlight html %}
<link rel="import" href="../polymer/polymer.html">
<polymer-element name="es6-object" attributes="name">
  <template>
    Hello {{ "{{foo + bar "}}}}
  </template>
  <script>
    (function () {
      var config = Object.create( {
        foo: 'bar'
      }, {
        bar: {
          value: 'baz'
        }
      } );
      Polymer( 'es6-object', config );
    }());
  </script>
</polymer-element>
<!-- Hello undefinedbaz -->
{% endhighlight %}

So that leaves `configurable`, `writable`, `enumerable`,
the Object methods: `freeze`, `seal`, and `preventExtensions`, 
and getters and setters.

`writable` seems to be the most generally helpful option.  Since Polymer's
web components keep references to objects in their registration, this 
technique could help prevent certain properties from changing.

{% highlight html %}
<link rel="import" href="../polymer/polymer.html">
<polymer-element name="es6-object" attributes="name">
  <template>
    Hello {{ "{{foo "}}}}
  </template>
  <script>
    (function () {
      var config = Object.create( {}, {
        foo: {
          value: 'bar',
          //change to true to see 'Hello baz'
          writable: false
        },
        ready: {
          value: function () {
            this.foo = 'baz';
          }
        }
      } );
      Polymer( 'es6-object', config );
    }());
  </script>
</polymer-element>
<!-- Hello bar -->
{% endhighlight %}

In Polymer, properties don't delete and `configurable` can't change that.

{% highlight html %}
<link rel="import" href="../polymer/polymer.html">
<polymer-element name="es6-object" attributes="name">
  <template>
    Hello {{ "{{foo "}}}}
  </template>
  <script>
    (function () {
      var config = Object.create( {}, {
        foo: {
          value: 'bar',
          writable: true,
          configurable: true
        },
        ready: {
          value: function () {
            var self = this;
            //this won't do anything
            setTimeout( function () {
              delete self.foo;
            }, 100 );
          }
        }
      } );
      Polymer( 'es6-object', config );
    }());
  </script>
</polymer-element>
<!-- Hello bar -->
{% endhighlight %}

The most pressing use for `enumerable` seems to be the use of
[mixins](http://pascalprecht.github.io/2014/07/14/inheritance-and-composition-with-polymer/), 
which allow for some extending of common objects.  Marking something as
non-enumerable means it won't get mixed-in.

{% highlight html %}
<link rel="import" href="../polymer/polymer.html">
<polymer-element name="es6-object" attributes="name">
  <template>
    Hello {{ "{{foo "}}}}
  </template>
  <script>
    (function () {
      var config = Object.create( {}, {
        foo: {
          value: 'bar',
          //change to true to see 'Hello bar'
          enumerable: false
        }
      } );
      Polymer( 'es6-object', Platform.mixin( {}, config ) );
    }());
  </script>
</polymer-element>
<!-- Hello -->
{% endhighlight %}

`Object.preventExtensions` works and could be a great help in preventing
unwanted additions to an element's model.

{% highlight html %}
<link rel="import" href="../polymer/polymer.html">
<polymer-element name="es6-object" attributes="name">
  <template>
    Hello {{ "{{foo "}}}}
  </template>
  <script>
    (function () {
      var config = Object.create( {}, {
        ready: {
          value: function () {
            //comment this out to see 'Hello bar'
            Object.preventExtensions( this );
            this.foo = 'bar';
          }
        }
      } );
      Polymer( 'es6-object', config );
    }());
  </script>
</polymer-element>
<!-- Hello -->
{% endhighlight %}

`Object.freeze` and `Object.seal` throw a `Uncaught NoModificationAllowedError`.  This
probably has something to do with the way properties get copied, not unlike why delete
fails with generic objects.

{% highlight html %}
<link rel="import" href="../polymer/polymer.html">
<polymer-element name="es6-object" attributes="name">
  <template>
    Hello {{ "{{foo "}}}}
  </template>
  <script>
    (function () {
      var config = Object.create( {}, {
        foo: {
          value: 'bar',
          writable: true
        },
        ready: {
          value: function () {
            Object.freeze( this );
            this.foo = 'baz';
          }
        }
      } );
      Polymer( 'es6-object', config );
    }());
  </script>
</polymer-element>
<!-- [Error] -->
{% endhighlight %}

Getters and setters sort of work.  In the example below, you see the
right `fullName` but if you change either name using the input field, 
the echo message won't update even though the first and last names updated 
on the model.  However, if you uncomment the lines where we bind to first 
and last name, it works as expected.

This means that using a setter circumvents Polymer's observance of the model
change and, as a result, you probably want to avoid setters for now.

{% highlight html %}
<link rel="import" href="../polymer/polymer.html">
<link rel="import" href="../paper-input/paper-input.html">

<polymer-element name="es6-object" attributes="firstName lastName">
  <template>
    <paper-input value="{{ "{{ fullName  "}}}}"></paper-input>
    <div>Hello {{ "{{ fullName  "}}}}</div>
    <!--<div>Firstname: {{ "{{ firstName  "}}}}</div>-->
    <!--<div>Lastname: {{ "{{ lastName  "}}}}</div>-->
  </template>
  <script>
    (function () {
      var person = {
        firstName: 'Jimmy',
        lastName: 'Smith',
      };
    
      Object.defineProperty( person, 'fullName', {
        get: function() {
          return this.firstName + ' ' + this.lastName;
      },
      set: function( name ) {
        var words = name.split(' ');
        this.firstName = words[0] || '';
        this.lastName = words[1] || '';
      }
    } );
    Polymer( 'es6-object', person );
    }());
  </script>
</polymer-element>
{% endhighlight %}