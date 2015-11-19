---
layout: post
title: "Up and running .NET on a MAC in 5 mins"
date: 2015-05-19 08:24:28 +1000
comments: true
categories: [.NET, DNX, OSX]
---

<strong> Installation Steps </strong>  :

        $ brew tap aspnet/dnx
        $ brew update
        $ brew install dnvm
        $ source dnvm.sh
        $ dnvm upgrade -u

If brew is returning an error copy and paste the below to install brew

        $ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"


<strong> Testing time </strong> :
        Type a simple hello world ( or copy and paste the below)

{% codeblock %}

 using System;

 public class HelloWorld
 {
    public static void Main (string[] args)
     {
         Console.WriteLine ("Hello World!");
     }
 }

{% endcodeblock %}


Compile :

        $ dmcs HelloWorld.cs

Run :

        $ dnx HelloWorld.exe


Done!



