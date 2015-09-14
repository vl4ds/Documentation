.. _groovy-for-smartthings:

Groovy With SmartThings
=======================

SmartThings runs Groovy in a sandboxed environment. This means that not all features of the Groovy programming language are available in SmartThings. This is because all SmartApps or Device Type Handlers are executed within the SmartThings ecosystem. Certain language features may present security or performance risks to the executing code and platform itself. In order to mitigate these risks, as well as to simplify the language, certain features discussed below have been removed from SmartThings.

Before we discuss the specifics of what is and what is not available to your SmartApps and Device Type Handlers, we'll first discuss how SmartThings makes several APIs available within your SmartApp or Device Type Handler. This is accomplished through inheritance, and while not necessary to understand for creating SmartThings code, it may help to shed light on what is happening behind the scenes.

How It Works
------------

One of the first things you'll notice when starting to develop with SmartThings, is that there are many methods available to you that do not require any import statements. In fact, it's rare to see import statements at all in SmartThings.

This is because every SmartApp or Device Type Handler is actually an instance of an abstract *Executor* class defined in the SmartThings platform. This *Executor* class defines several methods, and *delegates* to many other APIs. The result of this is that every SmartApp or Device Type Handler has available to it (through inheritance) a large number of methods without importing anything.

This model provides a simple framework in which you can develop your SmartApps and Device Type Handlers - all the necessary methods are simply available to call without needing to import anything.

Now that we understand (at least at a high level) how SmartApps and Device Type Handlers make various methods available, let's look at some of the things that are *not* allowed within SmartThings code. After that, we'll look at the entire whitelist of allowable classes.

Language Simplifications
------------------------

Classes and JARs
````````````````

As a SmartApp or Device Type Handler author, you cannot create your own classes, or import any custom JARs. While at first this may seem like a significant restriction, in practice you'll rarely find this to be the case. Because of the nature of SmartApps and Device Type Handlers, and the various methods available to you, the need to create your own classes or object structures is rarely needed.

There may be certain scenarios in which you discover your task would be easier if only you could import some third-party library or create your own helper class. In cases like these, reach out to us on the forums and let us know your specific use case. It's possible there already exists an API to do what you need, and if not, we may be able to get it added to SmartThings.

Restricted Methods
``````````````````

Because SmartThings code executes within its own ecosystem, there are a few methods that we restrict for security purposes. Many of these methods deal with Groovy's advanced metaprogramming concepts. Groovy metaprogramming allows developers to get and modify runtime information for objects. In SmartThings, this isn't necessary to do and is a potential security risk, so they are disabled.

Here are the methods that are not available in SmartThings. Trying to access these will result in a ``SecurityException`` at runtime.

- ``addShutdownHook()``
- ``execute()``
- ``getClass()``
- ``getMetaClass()``
- ``setMetaClass()``
- ``propertyMissing()``
- ``methodMissing()``
- ``invokeMethod()``
- ``mixin()``
- ``print()``
- ``printf()``
- ``println()``
- ``sleep()``


Other Notable Restrictions
``````````````````````````

There are a few other notable restrictions in SmartThings worth discussing:

- You cannot create your own threads.
- You cannot use ``System`` methods, like ``System.out()``
- You cannot create or access files.
- You cannot define closures outside of a method. Something like ``def squareItClosure = {it * it}`` is not allowed at the top-level, outside of a method body.

Allowed Classes
---------------

SmartThings also specifies a *whitelist* of allowed classes. Only classes included in this whitelist are available for use within SmartThings. Whenever a method is called (any method), SmartThings first checks to see that the *receiver* of the method (the the object the method is being called on) is in the allowable types whitelist.

You'll rarely find the need to instantiate any of these classes directly. In fact, it's rare in SmartThings to ever create a new object via the ``new`` operator. Most objects you work with will be available to you via callback parameters or other getter methods.

Here is the whitelist of available, non-SmartThings-specific types (i.e., Java, Groovy and third party library classes):

- ``ArrayList``
- ``BigDecimal``
- ``BigInteger``
- ``Boolean``
- ``Byte``
- ``ByteArrayInputStream``
- ``ByteArrayOutputStream``
- ``Calendar``
- ``Closure``
- ``Collection``
- ``Collections``
- ``Date``
- ``DecimalFormat``
- ``Double``
- ``Float``
- ``GregorianCalendar``
- ``HashMap``
- ``HashMap.Entry``
- ``HashMap.KeyIterator``
- ``HashMap.KeySet``
- ``HashMap.Values``
- ``HashSet``
- ``Integer``
- ``JsonBuilder``
- ``LinkedHashMap``
- ``LinkedHashMap.Entry``
- ``LinkedHashSet``
- ``LinkedList``
- ``List``
- ``Long``
- ``Map``
- ``MarkupBuilder``
- ``Math``
- ``Random``
- ``Set``
- ``Short``
- ``SimpleDateFormat``
- ``String``
- ``StringBuilder``
- ``StringReader``
- ``StringWriter``
- ``SubList``
- ``TimeCategory``
- ``TimeZone``
- ``TreeMap``
- ``TreeMap.Entry``
- ``TreeMap.KeySet``
- ``TreeMap.Values``
- ``TreeSet``
- ``URLDecoder``
- ``URLEncoder``
- ``UUID``
- ``XPath``
- ``XPathConstants``
- ``XPathExpressionImpl``
- ``XPathFactory``
- ``XPathFactoryImpl``
- ``XPathImpl``
- ``ZoneInfo``
- ``com.amazonaws.services.s3.model.S3Object``
- ``com.amazonaws.services.s3.model.S3ObjectInputStream``
- ``com.sun.org.apache.xerces.internal.dom.DocumentImpl``
- ``com.sun.org.apache.xerces.internal.dom.ElementImpl``
- ``groovy.json.JsonOutput``
- ``groovy.json.JsonSlurper``
- ``groovy.util.Node``
- ``groovy.util.NodeList``
- ``groovy.util.XmlParser``
- ``groovy.util.XmlSlurper``
- ``groovy.xml.XmlUtil``
- ``java.net.URI``
- ``java.util.RandomAccessSubList``
- ``org.apache.commons.codec.binary.Base64``
- ``org.apache.xerces.dom.DocumentImpl``
- ``org.apache.xerces.dom.ElementImpl``
- ``org.codehaus.groovy.runtime.EncodingGroovyMethods``
- ``org.json.JSONArray``
- ``org.json.JSONException``
- ``org.json.JSONObject``
- ``org.json.JSONObject.Null``
