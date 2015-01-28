State
=====

Recall that SmartApps are not always running, but rather execute according to a certain schedule or in response to events being triggered. But what if we need our SmartApp to retain some information between executions? 

SmartApps are all provided a ``state`` variable that will allow you to store data across executions.

Using SmartApp State
--------------------

To use SmartApp state, simply use the ``state`` variable that is injected into every SmartApp. You can think of it as a map that will persist its value across executions.

You can use SmartApp state to store strings, numbers, lists, boolean values, maps, etc. Behind the scenes, SmartThings serializes state data into JSON. This means that there are certain types that are not amenable to being stored in state. This is discussed further in the `State Limitations`_ section.

Typically SmartApps will use or update SmartApp state values in an event handler.

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

State Limitations
-----------------

SmartApp state is stored in JSON format. For most data types this works fine, but for more complex object types this may cause issues.

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

Examples
--------

Here are some SmartApps that make use of state. You can find them in the IDE along with the other example SmartApps.

- "Smart Nightlight" - shows using state to store time information.
- "Laundry Monitor" - uses state to store boolean state and time information.
- "Good Night" - shows using state to store time information, including constructing a Date object from a value stored in state. 