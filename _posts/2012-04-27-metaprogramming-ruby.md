---
layout: post
title: "Metaprogramming ruby"
category: 
tags: []
---
{% include JB/setup %}

Object model
=====

###class and objects

Instance variables live in objects, and methods live in
classes.

String.instance_methods == "abc".methods  # => true

String.methods == "abc".methods  # => false

classes are nothing but objects, class names are nothing but constants.

类也是对象，类名则是一些常数值。

Class有new/instance_methods/methods/superclass/class/ancestors方法

Classes and regular objects live together



###class and module
1. Usually, you pick a module when you mean it to be included somewhere (or maybe to be used as a Namespace (41)).
2. you pick a class when you mean it to be instantiated or inherited. 

###constants
all the constants in a program are arranged in a tree similar to a file system, 
where modules (and classes) are directories and regular constants are files.

###class definition
a Ruby class definition is actually regular code that runs.

* Keep in mind that a class is just a souped-up module.
* The role of the current class is taken by the class of self.

As soon as you enter a new scope, the previous bindings are simply replaced by a new set of bindings. 

定义一个类的时候，self指向当前类,bindings被当前类的bindings替代.


Class Macros
==========
    methods that modify classes
All the attr_*( ) methods are defined on class Module, so you can use them
whenever self is a module or a class. A method such as attr_accessor( ) is
called a Class Macro.

remember that an attribute is actually a pair of methods

###Monkey See, Monkey Patch
if you casually add bits and pieces of functionality to classes, you can end up with bugs like the one you just encountered. Some people would frown upon this kind of reckless patching of classes, and they would refer to the previous code
with a derogatory name: they’d call it a Monkeypatch.

###Around Aliases < Monkey Patch
(methods that wrap additional code around other methods).
You can write an Around Alias in three simple steps:
1. You alias a method.
2. You redefine it.
3. You call the old method from the new method.

> As usual, the more powerful the tricks you pull, the more testing of code you need to do!

{% highlight ruby %}
def self.deprecate(old_method, new_method)
    define_method(old_method) do |*args, &block|
        warn "Warning: #{old_method}() is deprecated. Use #{new_method}()."
        send(new_method, *args, &block)
    end
end
{% endhighlight ruby %}


###Singleton Method
* A method specific to a single object, is called a Singleton Method.
* living in the EigenClass of the object.

###Eigenclasses 
(also known as singleton classes) 
an object can have its own special, hidden class. 
That’s called the eigen-class of the object.

eigenclasses have only a single instance
(that’s why they’re also called singleton classes).and they can’t be inherited. 
More important, an eigenclass is where an object’s Singleton
Methods live


###class methods:
they’re Singleton Methods of a class. 

* Class methods are a special kind of Singleton Method

class methods are just Singleton Methods that live in the class’s
eigenclass, you can just open the eigenclass and define the
method in there:

{% highlight ruby %}
class MyClass
  class << self
    def my_method; end
  end
end
{% endhighlight ruby %}

eigenclass helper:
{% highlight ruby %}
class Object
  def eigenclass
    class << self;self;end
  end
end
{% endhighlight ruby %}

eigenclass and inheritance:
The superclass of the eigenclass is the eigenclass of the superclass.

###The Current Class:
Whenever you open a class with the class keyword(or a module with the module keyword),that class becomes the current class.
(the default home of the methods you define).

* In a class definition, the current object self is the class being
defined.
* The Ruby interpreter always keeps a reference to the current class
(or module). All methods defined with def become instance methods of the current class.
* In a class definition, the current class is the same as self—the class
being defined.
* If you have a reference to the class, you can open the class with
class_eval（） (or module_eval( )).


###class_eval:
    Module#class_eval( )
    (also known by its alternate name, module_eval( ))
    evaluates a block in the context of an existing class

#####class_eval( ) 
changes both self and the current class.
By changing the current class, class_eval( ) effectively reopens the class, just like the class keyword does.

#####instance_eval( ) 
only changes self.
in fact,instance_eval( ) also changes the current class: 
it changes it to the eigenclass of the receiver.

Method call
=========
When you call a method, Ruby does two things:

1. It finds the method. This is a process called `method lookup`.
2. It executes the method. To do that, Ruby needs something called
`self`.

###method lookup:
When you call a method, Ruby looks into the object’s class and finds the method there.
  
 * ancestors chain:
   The path of classes you just traversed is the ancestors chain of the class. 
  (The ancestors chain also includes modules)

 * To find a method, Ruby goes in the receiver’s class, and from there
it climbs the ancestors chain until it finds the method.

###module lookup:

 * When you include a module in a class (or even in another module),
Ruby creates an `anonymous class` that wraps the module and inserts the anonymous class in the chain, just above the including class itself
 
 * wrapper classes are called `include classes` (or sometimes proxy
classes).

 * class Object includes Kernel, so Kernel gets into every object’s ancestors chain.

 * if you add a method to Kernel, this Kernel Method will be available to all objects.


###Discovering self
Every line of Ruby code is executed inside an object—the so–called current object. 
The current object is also known as self, because you can access it with the self keyword.

* When you call a method, Ruby looks up the method by following the `one step to the right, then up` rule and then
executes the method with the receiver as self. 

###private method
* every time you call a private method, it must be on the implicit receiver—self.

###private rule
1.you need an explicit receiver to call a method on an object that is not yourself
2.private methods can be called only with an implicit receiver. 
SO you can only call a private method on yourself. 


Methods:
------------------
using either Dynamic Methods or a special method called method_missing()

###Calling Methods Dynamically:
my mentors told me that when you call a method, you’re actually sending a
message to an object.

###Dynamic Dispatch:
With send( ), the name of the method that you want to call becomes just a regular argument. You can
wait literally until the very last moment to decide which method to call,
while the code is running. This technique is called Dynamic Dispatch
Pattern Dispatch:
it filters methods based on a pattern in their names


###Defining Methods Dynamically:

    Module#define_method( ).
    There is also an Object#define_method( ) that defines a Singleton Method 

###Dynamic Method:
This technique of defining a method at runtime is called a Dynamic Method

###Ghost Method:
A message that’s processed by method_missing( ) looks like a regular call
from the caller’s side but has no corresponding method on the receiver’s
side. This is named a Ghost Method. 
Ghost Methods like rows_with_country( ) are just syntactic sugar; they
can’t do anything that a regular method couldn’t.
* don’t introduce more Ghost Methods than necessary


###Dynamic Proxies:
An object that catches Ghost Methods and forwards them to another ob-
ject, maybe wrapping some logic around the call, is called a Dynamic
Proxy.

###Blank Slate:
* Classes that inherit directly from BasicObject are automatically Blank Slates.

remove most inherited methods from your proxies right away.
The result is called a Blank Slate, a class that has fewer methods than
the Object class itself
{% highlight ruby %}
undef_method() / remove_method()
instance_methods.each do |m|
  undef_method m unless m.to_s =~ /^__|method_missing|respond_to?/
end
{% endhighlight ruby %}


blocks:
========
blocks are a powerful tool for controlling scope, meaning which variables and methods can be seen by which lines of code.

* Code that runs is actually made up of two things: the code itself and a set of bindings.
When code runs, it needs an environment: local variables, instance variables,
self. . . . 
* Since these entities are basically names bound to objects, you
can call them the bindings for short. 

Scope:
===========
###bindings and scope
when you create the block, you capture the local bindings.
A block captures the bindings that are around when you first define the block. You can also define additional bindings inside the block, but they disappear after the block ends.

The first time the program enters my_method( ), it opens a new scope and defines a local variable. 
Then the program exits the method, falling back to the top-level scope.


* “Whenever the program changes scope, some bindings are replaced by a new set of bindings.”

Granted, this doesn’t happen to all the bindings each and every time.
For example, if a method calls another method on the same object,
instance variables stay in scope through the call. 
In general, though,bindings tend to fall out of scope when the scope changes. In particular, local variables change at every new scope. (That’s why they’re “local”!)"

###scope gates:
There are exactly three places where a program leaves the previous
scope behind and opens a new one:

* Class definitions
* Module definitions
* Methods

Being less universally accessible, top-level instance variables are generally considered safer than global variables

The code in a class or module definition is executed immediately. 
Conversely, the code in a method definition is executed later, when you eventually call the method. 

不能从内看到外面的本地变量
###flat scope:
If you replace Scope Gates with methods, you allow one scope to see
variables from another scope. 
Technically, this trick should be called `nested lexical scopes`

* Class.new( ) is a perfect replacement for class. 
You can also define instance methods in the class if you pass a block to `Class.new( )`

* Instead of def, you can use `Module#define_method( )`

###shared scope:
No other method can see shared, because it’s protected by a Scope Gate
That’s what the define_methods( ) method is for. This smart way
to control the sharing of variables is called a `Shared Scope`.

###context probe:
you can call the block that you pass to instance_eval( ) a Context Probe, because it’s like a snippet of code that you dip inside an object to do something in there,but it also allows you to pass arguments to the block


###clean room:
Sometimes you create an object just to evaluate blocks inside it. An
object like that can be called a Clean Room:

```
env = Object.new
env.instance_eval &block
env.instance_eval &event
```
The instance variables in the setups and events are actually instance variables of the Object. 
This is the trick that allows setups to define variables for events


###Callable Objects:
####Deferred Evaluation:
you can create a Proc by passing the block to Proc.new. Later, you can
evaluate the block-turned-object with Proc#call( )

* you can use the &operator to convert the Proc to a block

####proc and lambda:

* In a lambda, return just returns from the lambda.
* In a proc, return behaves differently. Rather than return from the proc,
it returns from the scope where the proc itself was defined
* Call a lambda with the wrong arity, and it fails
with an ArgumentError. On the other hand, a proc fits the argument list
to its own expectations
p = ->(x) { x + 1 }(haskell way)

####method:
* By calling Object#method( ), you get the method itself as a Method object,
which you can later execute with Method#call( ).
* You can detach a method from its object with Method#unbind( ), which
returns an UnboundMethod object.





Objects and Classes Wrap-Up
===================
###What’s an object? 
It’s just a bunch of instance variables, plus a link to a class. 
The object’s methods don’t live in the object—they live in the object’s class, where they’re called the instance methods of the class.


###What’s a class? 
It’s just an object (an instance of Class), plus a list of instance methods and a link to a superclass. 
Class is a subclass of Module, so a class is also a module.

Like any object, a class has its own methods, such as new( ). 
These are instance methods of the Class class. 
Also like any object, classes must be accessed through references. 
You already have a constant reference to each class: the class’s name.




Summary
==============

* An object is composed of a bunch of instance variables and a link
to a class.
* The methods of an object live in the object’s class (from the point
of view of the class, they’re called instance methods).
* The class itself is just an object of class Class. The name of the
class is just a constant.
* Class is a subclass of Module. A module is basically a package of
methods. In addition to that, a class can also be instantiated (with
new( )) or arranged in a hierarchy (through its superclass( )).
* Constants are arranged in a tree similar to a file system, where
the names of modules and classes play the part of directories and
regular constants play the part of files.
* Each class has an ancestors chain, beginning with the class itself
and going up to BasicObject.
* When you call a method, Ruby goes right into the class of the
receiver and then up the ancestors chain, until it either finds the
method or reaches the end of the chain.
* Every time a class includes a module, the module is inserted in
the ancestors chain right above the class itself.
* When you call a method, the receiver takes the role of self.
* When you’re defining a module (or a class), the module takes the
role of self.
* Instance variables are always assumed to be instance variables of
self.
* Any method called without an explicit receiver is assumed to be a
method of self. 
* There are classes, eigenclasses, and modules.
There are instance methods, class methods, and Singleton Methods.



Seven rules of the Ruby object model:
-----------------
1. There is only one kind of object—be it a regular object or a module.
2. There is only one kind of module—be it a regular module, a class,
an eigenclass, or a proxy class.
3. There is only one kind of method, and it lives in a module—most
often in a class.
4. Every object, classes included, has its own “real class,” be it a
regular class or an eigenclass.
5. Every class has exactly one superclass, with the exception of Basi-
cObject (or Object if you’re using Ruby 1.8), which has none. This
means you have a single ancestors chain from any class up to
BasicObject.
6. The superclass of the eigenclass of an object is the object’s class.
The superclass of the eigenclass of a class is the eigenclass of the
class’s superclass. (Try repeating that three times, fast! Then look
back at Figure 4.5, on the preceding page, and it will all make
sense.)
7. When you call a method, Ruby goes “right” in the receiver’s real
class and then “up” the ancestors chain. That’s all there is to know
about the way Ruby finds methods.







