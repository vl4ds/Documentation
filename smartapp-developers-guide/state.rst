Storing Data
============

SmartApps and Device Handlers are all provided a ``state`` variable that will allow you to store data across executions.

In this guide, you will learn:

- How to store data across executions using the ``state`` property.
- A basic understanding of how ``state`` works.
- When ``state`` may not be the best solution, and what to use instead.

.. contents::

Overview
--------

Recall that SmartApps (and Device Handlers) are not always running, but rather execute according to a certain schedule or in response to events being triggered. But what if we need our application to retain some information between executions? We can use ``state`` to persist data across executions.

Here's a quick example showing how to work with state:

.. code-block:: groovy

  state.myData = "some data"
  log.debug "state.myData = ${state.myData}"

  state.myCounter = state.myCounter + 1

How it Works
------------

Each executing instance of a SmartApp or Device Handler has access to a simple, map-like storage mechanism through the ``state`` property.
When a SmartApp executes, it populates the ``state`` property from the backing store. The application can then interact with it, through simple map-like operations. 

When the application is finished executing, the values in ``state`` are written back to persistent storage. 

The contents of ``state`` are stored as a string, in JSON format. This means that anything stored in ``state`` must be serializable to JSON. 

.. tip::

  State is stored in JSON format. For most data types this works fine, but for more complex object types this may cause issues.

  This is particularly worth noting when working with dates. If you need to store time information, consider using an epoch time stamp, conveniently available via the ``now()`` method:

  .. code-block:: groovy

    def installed() {
      state.installedAt = now()
    }

    def someEventHandler(evt) {
      def millisSinceInstalled = now() - state.installedAt
      log.debug "this app was installed ${millisSinceInstalled / 1000} seconds ago"

      // you can also create a Date object back from epoch time:
      log.debug "this app was installed at ${new Date(state.installedAt)}"
    }

Using State
-----------

You can use ``state`` to store strings, numbers, lists, booleans, maps, etc. 
To use ``state``, simply use the ``state`` variable that is injected into every SmartApp and Device Handler. You can think of it as a map that will persist its value across executions.

As usual, the best way to describe code is by showing code itself. 

.. code-block:: groovy

  def installed() {
    // simple number to keep track of executions
    state.count = 0

    // we can store maps in state
    state.myMap = [foo: "bar", baz: "fee"]

    // booleans are ok of course
    state.myBoolean = true

    // we can use array index notation if we want
    state['key'] = 'value'

    // we can store lists and maps, so we can make some interesting structures
    state.myListOfMaps = [[key1: "val1", bool1: true],
                          [otherKey: ["string 1", "string 2"]]]

  }

  def someEventHandler(evt) {

    // increment by 1
    state.count = state.count + 1

    log.debug "this event handler has been called ${state.count} times since installed"

    log.debug "state.myMap.foo: ${state.myMap.foo}" // => prints "bar"

    // we can access state value using array notation if we wish
    log.debug "state['myBoolean']: ${state['myBoolean']}"

    // we can navigate our list of maps
    state.myListOfMaps.each { map ->
      log.debug "entry: $map"
      map.each {
        log.debug "key: ${it.key}, value: ${it.value}"
      }
    }  

Atomic State
------------

Since ``state`` is initialized from persistent storage when a SmartApp or Device Handler executes, and is written to storage only when the application is done executing, there is the possibility that another execution *could* happen within that time window, and cause the values stored in ``state`` to appear inconsistent.

Consider this scenario:

#. User has "Some FancyPants SmartApp" installed. It subscribes to every switch in the user's house, and does something really awesome (and time-consuming) when a switch is turned on or off. It uses ``state``, and the current contents of ``state.awesomeVar`` is "I haven't executed in a while".

#. A switch is turned on. An execution (let's call it "Execution 1") of the app is triggered, and all the state information is loaded from external storage into the ``state`` property. The app sets ``state.awesomeVar = "I'm Execution 1!"``.

#. Another switch is turned on *before* "Execution 1" is finished. We'll call this "Execution 2". The application reads the value of ``state.awesomeVar``, and sees that it is "I haven't executed in a while" (not the value that was set by "Execution 1"!). This execution sets ``state.awesomeVar = "I'm Execution 2"``.

#. "Execution 1" finishes. The contents of ``state`` are written back to external storage, including the value in ``state.awesomeVar`` ("I'm execution 1!").

#. "Execution 2" finishes. The contents of ``state`` are written to external storage ("I'm execution 2!").

#. "Execution 3" starts, and reads the ``state.awesomeVar`` value. It sees the value of "I'm Execution 2".

To avoid race conditions like this, you can use ``atomicState``. ``atomicState`` writes to the data store when a value is *set*, and reads from the data store when a value is *read* - not just when the application execution initializes and completes. 

.. important::
  
  Using ``atomicState`` instead of ``state`` incurs a higher performance cost, since external storage is touched on read and write operations, not just when the application is initialized or done executing.

  Use ``atomicState`` only if you are sure that using ``state`` will cause problems. 

Examples
--------

Here are some SmartApps that make use of state. You can find them in the IDE along with the other example SmartApps.

- "Smart Nightlight" - shows using state to store time information.
- "Laundry Monitor" - uses state to store boolean state and time information.
- "Good Night" - shows using state to store time information, including constructing a Date object from a value stored in state. 