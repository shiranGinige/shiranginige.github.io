---
layout: post
title: "C# to javascript : classes"
date: 2015-01-21 08:22:48 +1100
comments: true
categories: CSharp,JavaScript,Classes
---


<h3>Show me the classes or I won't do it ! </h3>

One of the first questions that any C# developer would come across is, where are the "Classes". Let's take a look at a typical
class implementation in C# and see how we achieve conceptually the same thing with JavaScript.

 A simple Class in C#

 {% highlight csharp %}
 public class Cart
 {
   private IList<OrderLine> _orderLineItems;

   public Cart()
   {
     _cartItems = new List<OrderLine>();
   }

   public void AddItem(OrderLineItem orderLineItem)
   {
    _cartItems.Add(orderLineItem);
   }
 }

//usage


 var cart = new Cart();
 cart.AddItem(new OrderLineItem(){ProductCode="QWER1234" , Quantity=3});

{% endhighlight %}



Class in Javascript


{% highlight javascript %}

function Cart() {

    var cartItems = [];

    this.addItem = function (orderLineItem) {
        cartItems.push(orderLineItem);
    }

    this.Print = function () {

        for (var i = 0; i < cartItems.length; i++) {
            console.log("Quantity of : " + cartItems[i].Quantity);
        }
    }
}

//usage

var cart = new Cart();

cart.addItem({"ProductCode": "Qwer1234", "Quantity": "3"});

cart.Print();

{% endhighlight %}


As you can see, there is no keyword called  'class' in JavaScript, but use functions to achieve the structure we want.

This is called the constructor pattern. Unlike a typical JavaScript function UpperCase convention is followed when naming
the constructor function. The function should 'always' be called with the new keyword (mind you it is possible to call the
constructor function without the new keyword and it would lead to unprecedented results.

David Crawford explains what happens when you don't include new keyword it in his book JavaScript : The good parts (page 49)

If you forget to include the new prefix when calling a constructor function,
then this will not be bound to the new object.Sadly, this will be bound to the global object,
so instead of augmenting your new object, you will be clobbering global variables. That is really bad. There is no compile warning, and there is no runtime warning. (page 49)


1. Constructor function should follow the UpperCase convention
2. Should always use the new keyword to invoke a function




