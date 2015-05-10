---
layout: post
title: "C# to javascript : objects"
date: 2015-01-23 08:36:08 +1100
comments: true
categories: JavaScript,Objects
---

The best thing about Javascript is it's inherently easy way to deal with objects.

{% highlight javascript %}
var order = {productCode:"XYZ" , quantity=3};
{% endhighlight %}

Done. Here you have your order object! No need to define classes or nothing.

How do you access the properties? There are two ways...


    1. Dot notation , i.e => order.productCode;

    2. Subscript notation, i.e => order["productCode"];



<h3>Objects are more than that</h3>

In JavaScript, pretty much everything is an object. All primitive types ( String, Boolean, Number,etc) are objects

You can even define a full-on object with methods , on the fly ( to hell with classes , eh?)

{% highlight javascript %}

var cart = {
    items: [],
    addItem : function(item){
        this.items.push(item);
        console.log(this.items);
    }
};

cart.addItem({Product:'QWER',Quantity:12});

{% endhighlight %}


--TODO--