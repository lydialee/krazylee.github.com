---
layout: post
title: "var javascript={};"
category: 
tags: []
---
{% include JB/setup %}
一次团队内部的分享，本意是js入门, 结果....

同步的[slides](http://krazylife.net/slides/javascript/)

##Background
Javascript由网景和Sun两家公司一起携手推向市场.

必须"看上去与Java足够相似".

> Do not let Marketing name your language.

- Make it easy to copy/paste snippets of code
- Tolerate “minor” errors (missing semicolons)
- Simplified onclick, onmousedown
- Pick a few hard-working, powerful primitives
- First class functions for procedural abstraction
- Objects everywhere, prototype-based(object based)
- Leave all else out!

but....

##Variable
###variables
####data type(5):
Only five primitive types are not objects: (object-like,the java way):

- number 
{% highlight javascript %}
var a = 10;
var b = 1e3;
var c = NaN;
typeof c == "number"; => true
{% endhighlight javascript %}
- String
{% highlight javascript %}
var one = "first";
var two = "second";
var three = one + two; => "firstsecond"
{% endhighlight javascript %}
- Boolean
{% highlight javascript %}
==   "3"  == 3 //判断前, 类型转换 
===  "3" !== 3 //值与类型均相同 
{% endhighlight javascript %}
- undefined
{% highlight javascript %}
var o = {};
o.nonExist;
{% endhighlight javascript %}
- null

####data convertion:
{% highlight javascript %}
parseInt("123abc")
parseFloat("0.5aaa")
!!"numberOne"
3 + ""
{% endhighlight javascript %}

###*data types wrap-up*:
a few primitive types,everything else is an object
- Number
- String
- Boolean
- Undefined and null

##Function(first-class object)
functions:
objects,invokable
call apply


###Scope
全局变量
lexical function scope

##Object
Two main types of objects:
#### Native:Described in the ECMAScript standard
- built-in : for example, Array, Date
- user-defined: var o = {};
#### Host:Defined by the host environment 
- the browser environment
- node(nodejs is an implemention of commonJS)

Define
=======
##How to define an variable

number,string,boolean,undefined,null

var

*single var pattern*

##How to define an object
object:

objects are hashes.array are objects

##How to define a function
{% highlight javascript %}
function myFunc(a,b){
	return a + b;	
}

var myFunc = function(a,b){
	return a + b;	
};
var myFunc = new Function("a","b","return a + b")
{% endhighlight javascript %}

##How to define a class( 封装与继承）
prototype(useful with constructor)

##constructor:
called with new

return an object

##inheritance:
prototypal

classical




