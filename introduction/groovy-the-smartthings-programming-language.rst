Groovy – The SmartThings Programming Language
=============================================

The SmartApp programming language is a `domain-specific language`_  (DSL) based on the `Groovy programming language`_.

What is Groovy?
--------------

Groovy is an object-oriented_ programming language for the `Java platform`_.
It is a `dynamic language`_ with features similar to those of Python_, Ruby_,
Perl_, and Smalltalk_. 

It can be used as a `scripting language`_ for the Java Platform, is dynamically compiled to `Java Virtual Machine`_ (JVM) bytecode_,  and interoperates with other Java code and libraries. 

Groovy uses a Java-like `bracket syntax`_. Most Java code is also syntactically valid Groovy.

Why Groovy?
-----------

By basing our first programming language on Groovy, we provide the benefits of a dynamic language with the scalability and performance of Java. Longer term, we hope to enable other programming languages in the SmartThings Cloud so that you can ultimately chose from several supported languages. Likely candidates (no promises) for additional languages include Ruby, JavaScript, Clojure, and
Python.

For more documentation on the syntax, structure, and capabilities of Groovy,
visit http://groovy.codehaus.org/Documentation. If you're new to Groovy, we recommend checking out the `Groovy Quick Start`_ and the `Groovy Beginners Tutorial`_. 

Note, however, that because of the application “sandboxing” that we do in the SmartThings Cloud, some features of Groovy are disabled for security reasons. We will discuss this more in the `Groovy Sandboxing`_ topic below.

 
Groovy Sandboxing
-----------------

SmartThings runs with a sandboxed environment. This means that not all features of the Groovy programming language are availble to SmartThings developers. This is done for reasons of security and simplicity. 

Here are some of the restrictions:

**Class restrictions**

You cannot define your own classes within the SmartThings platform, or include any of your own classes. You cannot do this:

.. code-block:: groovy

    class Foo {
        def list
    }

SmartThings allows only certain classes to be used in the sandbox. A SecurityException will be thrown if using a class that is not allowed.

----

**Closure restrictions**

One of the more powerful features of the Groovy programming language is its support of closures_. Closures (among other things) allow for expressive and succinct programming expression.

In SmartThings, you cannot define closures outside of methods. For example, you cannot do this:

.. code-block:: groovy

    def squareItClosure = {it * it} 

----

**Builders**

If you're familiar with Groovy, you likely know about the `Groovy builder pattern`_. Builders offer a nice way to build a hierarchichal data structure. 

Due to the way builders are implemented using closures, they will not work in SmartThings. This means things like XMLBuilder and JSONBuilder are not available to use.

----

**Restricted methods**

You cannot use any of the following methods in SmartThings:

- getClass
- getMetaClass
- setMetaClass
- propertyMissing
- methodMissing
- invokeMethod
- println
- sleep (you can use the SmartThings *pause(milliseconds)* method, for anything less than 15000 milliseconds)

----

**Restricted properties**

You cannot use any of the following properties in SmartThings:

- class
- metaClass

----

**Other restrictions**

A few other things you cannot do in SmartThings:

- Create and use new threads
- Use System methods, like System.out





.. _domain-specific language: http://en.wikipedia.org/wiki/Domain-specific_language
.. _Groovy programming language: http://groovy.codehaus.org/
.. _object-oriented: http://en.wikipedia.org/wiki/Object-oriented_programming
.. _Java platform: http://en.wikipedia.org/wiki/Java_platform 
.. _dynamic language: http://en.wikipedia.org/wiki/Dynamic_programming_language 
.. _Python: http://en.wikipedia.org/wiki/Python_(programming_language) 
.. _Ruby: http://en.wikipedia.org/wiki/Ruby_%28programming_language%29 
.. _Perl: http://en.wikipedia.org/wiki/Perl 
.. _Smalltalk: http://en.wikipedia.org/wiki/Smalltalk
.. _scripting language: http://en.wikipedia.org/wiki/Scripting_language
.. _Java Virtual Machine: http://en.wikipedia.org/wiki/Java_Virtual_Machine
.. _bytecode: http://en.wikipedia.org/wiki/Bytecode
.. _bracket syntax: http://en.wikipedia.org/wiki/Curly_bracket_programming_language
.. _closures: http://en.wikipedia.org/wiki/Closure_%28computer_programming%29
.. _Groovy Quick Start: http://groovy.codehaus.org/Quick+Start
.. _Groovy Beginners Tutorial: http://groovy.codehaus.org/Beginners+Tutorial
.. _Groovy Collections: http://groovy.codehaus.org/JN1015-Collections
.. _Groovy Closures: http://groovy.codehaus.org/Tutorial+2+-+Code+as+data%2C+or+closures
.. _Groovy builder pattern: http://groovy.codehaus.org/Builders
