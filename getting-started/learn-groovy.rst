.. _learning-groovy:

Groovy for SmartThings
======================

SmartThings uses the Groovy programming language. If you've programmed before, you can learn Groovy easily.

The Groovy programming language is documented at http://www.groovy-lang.org/documentation.html. This guide will familiarize you with Groovy and its use in SmartThings, but is not a complete reference for the language.

Groovy Basics
-------------

Groovy is an object-oriented programming language for the Java platform. It is a dynamic language with features similar to those of Python, Ruby, Perl, and Smalltalk.

If you are familiar with languages like Java, C/C++, Python, Ruby, or JavaScript, you will see many similarities in Groovy.

Installing Groovy
`````````````````

The best way to get familiar with Groovy is by installing it and experimenting. SmartThings development does not require you to have a copy of Groovy installed, since SmartThings code is executed within SmartThings infrastructure, but having a local copy of Groovy is useful for learning.

Head over to the `Groovy Documentation <http://www.groovy-lang.org/documentation.html>`__ site and follow the Getting Started guides for downloading and installing Groovy (the rest of the Getting Started material is pretty awesome too, and definitely worth a read).

We make heavy use of the Groovy Console to test things out, and recommend you do to.

.. note::

    In the code snippets below, you'll see a method ``assert()`` used often. This method is built in to Groovy, and we use it to verify assumptions. If the value passed to ``assert()`` is not true, the program will terminate. This lets us test out our code easily.

    For example, ``assert true`` is valid, and the program will continue. Anything that evaluates to false will cause the program to halt, so ``assert false`` will terminate with an informative message.

    While useful for learning, it's important to note that ``assert()`` is **not available** for you to use in SmartThings code. For security and performance reasons, SmartThings runs in a sandboxed environment that restricts access to certain features. This will be discussed in more detail later, but ``assert()`` is still useful in learning Groovy in the Groovy Console or other non-SmartThings applications.

Objects
```````

In Groovy, everything is an object. Objects have *methods* and *properties*.

Methods are the things the object can do, and similar to other languages, are invoked with parentheses ``()`` that may contain arguments.

.. code-block:: groovy

    // calling method doSomething on someObject
    someObject.doSomething()

    // calling method doSomethingElse with one argument
    someObject.doSomethingElse("a string argument")

    // get the property named someProperty on someObject
    someObject.someProperty

Comments
````````

Groovy supports single line comments:

.. code-block:: groovy

    // this is a single line comment
    // each line requires slashes
    def myNum = 2  // comments can also come at the end of a statement

... and also multi-line comments:

.. code-block:: groovy

    /* this is a comment that
       spans multiple lines.*/
    def myNum = 2

Optionally Typed
````````````````

Groovy is an **optionally typed** language. The following are both valid Groovy:

.. code-block:: groovy

    // explicit typing
    Integer myInt = 5

    // using def
    def myInt2 = 42

In Groovy, we can use ``def`` in place of an explicit type. Behind the scenes, anything created with ``def`` is assigned to the Java type ``Object`` (remember that Groovy ultimately is dynamically compiled to Java bytecode).

THIS IS NOT ENTIRELY TRUE WITH PRIMITIVES (INCLUDING THE EXAMPLE ABOVE). NEED TO REFINE/CLARIFY!

Why use ``def`` instead of explicit types? While not required, ``def`` is commonly used in Groovy (and in SmartThings) because it provides greater flexibility and readability.

Consider this strongly typed example:

.. code-block:: groovy

    String addThem(String str1, String str2) {
        return str1 + str2
    }

    String added = addThem("Smart", "Things"); // returns "SmartThings"
    String added2 = addThem(1, 2);             // fails!

In the example above, ``addThem()`` is defined to accept two ``String`` parameters. When we try to invoke ``addThem()`` with two numbers, we will get a ``MissingMethodException``, because there is no method defined. You might see output like this if you tried to run the above code:

.. code-block:: bash

    groovy.lang.MissingMethodException: No signature of method: Script1.addThem() is applicable for argument types: (java.lang.Integer, java.lang.Integer) values: [1, 2]
    Possible solutions: addThem(java.lang.String, java.lang.String)
    at Script1.run(Script1.groovy:7)


If we use ``def`` instead of an explicit type, we can take advantage of something called `duck typing <https://en.wikipedia.org/wiki/Duck_typing>`__. Put simply, duck typing is the principle that if it walks like a duck and quacks like a duck, then it's a duck. In programming terms, this means that if an object supports certain properties or methods, then we can use those regardless of its type.

To illustrate this with an example, consider the above example refactored to use ``def``:

.. code-block:: groovy

    def addThem(str1, str2) {
        return str1 + str2
    }

    def added = addThem("Smart", "Things")
    assert added == "SmartThings"

    def added2 = addThem(4, 2)
    assert added2 == 6


Omitting the explicit type information in favor of ``def`` allows us to build flexible programs without getting bogged down in ensuring we have all our typing information correct. This is particularly useful for smaller programs, which is what you will be writing with SmartThings.

Strict statically typed languages like Java determines the method that will be called at *compile time*. Groovy determines the methods to invoke at *runtime*, using something called multi-methods or dynamic dispatch. You can read more about multi-methods `here <http://www.groovy-lang.org/differences.html#_multi_methods>`__ in the Groovy documentation.

Optional Semicolons
```````````````````

Semicolons are optional in Groovy, and generally not used:

.. code-block:: groovy

    def someString = "this statement has a semicolon";
    def someOtherString = "this one does not"

Operators
`````````

Groovy supports all the typical operators, such as arithmetic operators, assignment operators, and relational operators:

.. code-block:: groovy

    assert 1 + 2 == 3
    assert 1 < 2

    def a = 1
    def b = a += 2
    assert a == 3

    def c = 4
    def d = c++
    assert d == 5

There are many more Groovy operators documented `here <http://docs.groovy-lang.org/latest/html/documentation/#groovy-operators>`__. There are also a few awesome operators that really make Groovy powerful; those will be discussed later in the Tips & Best Practices section below.

Optional Parentheses
````````````````````

When invoking methods, parentheses are *sometimes* optional. Methods that do not accept any parameters must include the parentheses.

.. code-block:: groovy

    def myMethod() {
        // ...
    }

    def myOtherMethod(someArg1, someArg2) {
        // ...
    }

    myMethod()          // OK
    myMethod            // error
    myOtherMethod(2, 3) // OK
    myOtherMethod 4, 5  // OK

Strings
```````

Strings can be defined using double quotes or single quotes:

.. code-block:: groovy

    def a = "some string"
    def b = 'another string'

Strings defined with double quotes support interpolation. This allows us to substitute any Groovy expression into a String at the specified location. Interpolation is achieved using the ``${}`` syntax:

.. code-block:: groovy

    def name = "Your Name"
    def greeting = "Hello, ${name}"
    assert "Hello, Your Name" == greeting

Of course, more interesting interpolations are possible. Any expression can be placed inside the ``${}``:

.. code-block:: groovy

    def name = "Your Name"
    def greeting = "Hello, ${name.toUpperCase()}"
    assert "Hello, YOUR NAME" == greeting

You can also use the ``$`` without the ``{}`` for simple property substitutions or simple dotted expressions:

.. code-block:: groovy

    def name = "Your Name"

    // can omit the {} here
    def greeting = "Hello, $name"
    assert "Hello, Your Name" == greeting

    def person = [firstName: 'Walter', lastName: 'Sobchak']
    def greeting = "Hello, $person.firstName $person.lastName"

You'll see String interpolations a lot in SmartThings. You can read more about Strings `here <http://docs.groovy-lang.org/latest/html/documentation/#all-strings>`__.

Lists and Maps
``````````````

Groovy supports the typical collection structures like Lists and Maps in an easy-to-use way.

Here are some examples showing how to work with Lists in Groovy:

.. code-block:: groovy

    // simple list of Numbers
    def myList = [2, 3, 5, 8, 13, 21]

    // use the << operator to append items to a list
    myList << 34
    assert myList == [2, 3, 5, 8, 13, 21, 34]

    // get elements in a list
    // first element is at index 0
    assert 8 == myList[3]

    // can use negative index to start from the end
    assert 21 == myList[-2]

    // lists can support different types of data
    def myMixedList = [1, "two", true]

Maps are similarly straightforward:

.. code-block:: groovy

    // simple map of key/value pairs
    def myMap = [key1: "value1", key2: "value2"]

    // can get value for a key with the "." notation:
    assert "value1" == myMap.key1

    // can also get the value using subscript notation:
    assert "value2" == myMap['key2']

    // a list of maps
    def listOfMaps = [[key1: "val1", key2: "val2"],
                      [key1: "another val", key2: "and another"]]
    assert "another val" == listOfMaps[1].key1

Control Structures
``````````````````

Groovy supports the conditional if/else if/else syntax as you'd expect:

.. code-block:: groovy

    if (...) {
        ...
    } else if (...) {
        ...
    } else {
        ...
    }

You can also use the ``switch`` statement to handle possible values conditionally:

.. code-block:: groovy

    def deviceDescription = "presence: 1"
    def result = ""

    switch (deviceDescription) {
        case "presence: 0":
            result = "not present"
            break
        case "presence: 1":
            result = "present"
            break
        default:
            result = "unknown"
    }

    assert "present" == result

Looping is also similar to Java or C:

.. code-block:: groovy

    def result = ""
    for (int i = 0; i < 3; i++) {
        result += "Z"
    }
    assert "ZZZ" == result

You can also use the for/in loop when working with collections:

.. code-block:: groovy

    def next = 0
    for (i in [8, 13]) {
        next += i
    }
    assert next == 21

Exception Handling
``````````````````

Properties and Methods
``````````````````````

<include info about optional parentheses, getter methods as property access, and optional return statements>

Methods and Properties
``````````````````````

For the most part, methods are defined and invoked as in other modern languages. There are some notable enhancements that are worth noting, however.

First, the basics:

.. code-block:: groovy

    // arguments types are optional:
    def asMap(arg1, arg2) {
        return [arg1: arg2]
    }
    assert [key: "val"] == asMap("key", "val")

    // can use typed arguments as well
    def asMapWithTypedArgs(String arg1, String arg2) {
        return [arg1: arg2]
    }
    assert [key: "another val"] = asMap("key", "another val")

.. tip::

    The above method definition is the same as if we used the ``def`` keyword for each argument (``def asMap(def arg1, def arg2)``). The ``def`` adds additional clutter to our method signature, so we omit it.

Optional Parameters
```````````````````

Closures
````````

.. code-block:: groovy

    // change this to be original!
    def pickEven(n, block) {
        for (int i=2; i <= n; i += 2) {
            block(i)
        }
    }

    pickEven(10, {println it})


Could also be written as:

.. code-block:: groovy

    pickEven(10) {
        println it
    }

When a method accepts a closure (more on closures below), and it is the last argument, we typically omit the parentheses for greater readability:

.. code-block:: groovy

    def myMethod(arg1, closure) {
        ...
    }

    myMethod(2, {it.something()}) // OK, but not preferred

    myMethod(2) {      // preferred form, with closure outside of parentheses
        it.something()
    }

Collections
```````````

Groovy Truth
````````````

Groovy has some special definitions for what is true and what is false. It's worth understanding these definitions, as they become very valuable in writing concise, expressive Groovy code.

Boolean values behave as you'd expect:

.. code-block:: groovy

    def t = true
    def f = false

    assert t
    assert !f

If an object reference is null, it will evaluate to false:

    def obj
    assert !obj

This allows us to remove some boilerplate code around null checks. If you're familiar with Java, you have probably seen code like this:

.. code-block:: groovy

    if (obj != null) {
        // ...
    }

In Groovy, we can simply do:

.. code-block:: groovy

    if (obj) {
        // ...
    }

Strings also provide some handy truthiness:

.. code-block:: groovy

    def str1 = ""
    def str2 = "some string"

    assert !str1  // empty strings are false
    assert str2

Collections also support reasonable boolean values - empty collections evaluate to ``false``:

.. code-block:: groovy

    def list1 = [1, 2, 3]
    def list2 = []
    def map1 = ['myKey': 'myValue']
    def map2 = [:]

    assert list1
    assert !list2   // empty list is false
    assert map1
    assert !map2    // empty map is false

Back to Java, you may be familiar with writing code like this:

.. code-block:: java

    Map<String, String> myMap = someMethodThatReturnsAMap();
    if (myMap != null && !myMap.isEmpty()) {
        // ...
    }

That's a lot of noise in the code just to check that the map is not empty. With Groovy, this becomes much more straightforward:

.. code-block:: groovy

    def myMap = someMethodThatReturnsAMap()
    if (myMap) {
        // here we know that the map is not null, and contains items.
    }

There's more to know about Groovy truth, but the above should get you through 99% of the code you'll see and write with SmartThings. Read more about Groovy truth in the Groovy documentation `here <http://docs.groovy-lang.org/latest/html/documentation/#Groovy-Truth>`__.

Tips and Best Practices
-----------------------

Safe Navigation Operator
`````````````````````````

Using Groovy's *safe navigation operator*, you can navigate object structures without fear of getting a ``NullPointerException`` on a null object.

Suppose we have a property named ``location``, that also has a method ``getHelloHome()``. Further, suppose that the object returned by ``getHelloHome()`` has a method named ``getPhrases()``. Ultimately, we want to get the phrases.

We could do:

.. code-block:: groovy

    def phrases = location.getHelloHome().getPhrases()

But, what if ``getHelloHome()`` returns null? We'd then get a ``NullPointerException`` at runtime when trying to call ``getPhrases()`` on a null object.

If you're not familiar with Groovy, you might try something like this to avoid that:

.. code-block:: groovy

    def hh = location.getHelloHome()
    def phrases
    // recall that non-null objects are "true"
    if (hh) {
        phrases = hh.getPhrases()
    }

That works, and is valid Groovy, but we can do better. Using the safe navigation operator (``?.``), we can safely traverse the object graph. If any objects are null, the method simply will not be invoked and ``null`` will be returned.

This results in much cleaner code:

.. code-block:: groovy

    def phrases = location.getHelloHome()?.getPhrases()

In this example, if ``getHelloHome()`` is not null, we'll call the ``getPhrases()`` method on it. If it does return null, the whole expression simply returns ``null``.

If there's ever a chance of running into a ``NullPointerException``, use the safe navigation operator to safely (and concisely) avoid it.

Use the Groovy Collections
``````````````````````````

Spread Operator
```````````````

Place Closure Parameters Outside Parentheses
````````````````````````````````````````````

Groovy with SmartThings
-----------------------

Getting Help
------------
