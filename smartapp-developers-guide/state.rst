.. _storing-data:

State - Storing Data
====================

SmartApps and Device Handlers are all provided a ``state`` variable that will allow you to store data across executions.

.. warning::

    As discussed in the documentation below, ``state`` and ``atomicState`` should **never** be used in the same SmartApp.

    Doing so can cause inconsistencies or even loss of data.
    At some point, this may be enforced through a compile-time check to prevent making this mistake.

----

Overview
--------

Recall that SmartApps (and Device Handlers) are not always running, but rather execute according to a certain schedule or in response to events being triggered. But what if we need our application to retain some information between executions? We can use ``state`` to persist data across executions.

Here's a quick example showing how to work with state:

.. code-block:: groovy

    state.myData = "some data"
    log.debug "state.myData = ${state.myData}"

    state.myCounter = state.myCounter + 1

----

.. _state_how_it_works:

How it Works
------------

Each executing instance of a SmartApp or Device Handler has access to a simple, map-like storage mechanism through the ``state`` property.
When an application executes, it populates the ``state`` property from the backing store. The application can then interact with it, through simple map-like operations.

When the application is finished executing, the values in ``state`` are written back to persistent storage.

.. important::

  When an application stores data in ``state``, or reads from it, it is only modifying (or querying) the local ``state`` instance variable within the running SmartApp or Device Handler. Only when the application is done executing are the values written to persistent storage.

The contents of ``state`` are stored as a JSON string. This means that anything stored in ``state`` must be serializable to JSON.

.. tip::

  State is stored in JSON format; for most data types this works fine, but for more complex object types this may cause issues.

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

----

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

----

.. _atomic_state:

Atomic State
------------

.. note::

    Atomic State is currently only available for SmartApps. Device Handlers do not support Atomic State.

Since ``state`` is initialized from persistent storage when a SmartApp executes, and is written to storage only when the application is done executing, there is the possibility that another execution *could* happen within that time window, and cause the values stored in ``state`` to appear inconsistent.

Consider the scenario of a SmartApp that keeps a counter of executions. Each time the SmartApp executes, it increments the counter by 1. Assume that the initial value of ``state.counter`` is ``0``.

1. An execution ("Execution 1") occurs, and increments ``state.counter`` by one:

.. code-block:: groovy

  state.counter = state.counter + 1 // counter == 1

2. Another execution ("Execution 2") occurs *before "Execution 1" has finished*. It reads ``state.counter`` and increments it by one.

.. code-block:: groovy

  state.counter = state.counter + 1 // counter == 1!!!

Because "Execution 1" hasn't finished executing by the time that "Execution 2" begins, the value of ``counter`` is still 0!

Additionally, because the contents of ``state`` are only persisted when execution is complete, it's also possible to inadvertently overwrite values (last finished execution "wins").

To avoid this type of scenario, you can use ``atomicState``. ``atomicState`` writes to the data store when a value is *set*, and reads from the data store when a value is *read* - not just when the application execution initializes and completes. You use it just as you would use ``state``:

.. code-block:: groovy

  atomicState.counter = atomicState.counter + 1.

.. important::

  Using ``atomicState`` instead of ``state`` incurs a higher performance cost, since external storage is touched on read and write operations, not just when the application is initialized or done executing.

  Use ``atomicState`` only if you are sure that using ``state`` will cause problems.

  It's also worth noting that you should **not** use both ``state`` and ``atomicState`` in the same SmartApp. Doing so will likely cause inconsistencies in in state values.

----

.. _state_size_limit:

Storage Size Limitations
------------------------

The amount of data that can be stored in ``state`` or ``atomicState`` is limited to 100,000 characters per installed app.

This limit may be reduced in the future based on further analysis (any reduction will be communicated in advance), and only a `very` small number of apps will be potentially impacted.

To get the character size of ``state`` or ``atomicState``, you can do:

.. code-block:: groovy

    def stateCharSize = state.toString().length()

When the character limit has been exceeded, a ``physicalgraph.exception.StateCharacterLimitExceededException`` will be thrown.

.. important::

    Remember that when using ``state``, the contents are written to the external data store when the app is finished executing - not immediately on write/read from the object.

    This means that if the character limit is exceeded for ``state``, you won't be able to handle a ``StateCharacterLimitExceededException`` in your code - it will only be visible in the logs.

    If using ``atomicState``, which reads and writes to the external data store when the object is updated or accessed, you will be able to handle a ``StateCharacterLimitExceededException`` in your code.

    Additional helper methods to get the remaining available size and the character limit will be added in a future release.

----

.. _state_using_collections:

Using Collections in State
--------------------------

When storing collections in State, things are pretty straightforward.
Simply assign the collection to ``state``, and update entries as needed:

.. code-block:: groovy

    def initialize() {
        state.myMap = ["key1": "val1"]
        log.debug "state: $state"
        state.myMap.key1 = "UPDATED"
        log.debug "state: $state" // state is now [key1: UPDATED]
    }

Updating collections stored in Atomic State is a little trickier - following the same pattern as above **will not work**.
Instead, you will need to assign the collection to a local variable, make changes as needed, then assign it back to ``atomicState``.
Here's an example:

.. code-block:: groovy

    def initialize() {
        atomicState.myMap = [key1: "val1"]
        log.debug "atomicState: $atomicState"

        // assign collection to local variable and update
        def temp = atomicState.myMap
        // update existing entry
        temp.key1 = "UPDATED"
        // add new entry
        temp.key2 = "val2"

        // assign collection back to atomicState
        atomicState.myMap = temp
        log.debug "atomicState: $atomicState"
    }

This applies to all collection operations on items stored in Atomic State (adding, removing, modifying, etc).

----

Best Practices
--------------

- Only data that can be serialized to JSON can be stored in ``state`` or ``atomicState``.
- Remember that the contents of ``state`` are only written to external storage when the SmartApp or Device Handler finishes executing. All reads/writes from ``state`` are done on the in-memory object until app execution concludes. The contents of ``atomicState`` are written to external storage when a value changes.
- Use ``state`` unless you have demonstrated that ``state`` will cause consistency issues (as discussed in the :ref:`atomic_state` section). Using ``atomicState`` incurs a performance cost greater than ``state``.
- Never use both ``atomicState`` and ``state`` in the same SmartApp.
- ``atomicState`` is not available to Device Handlers.
- Don't store too much in ``state`` or ``atomicState``. The limit is 100,000 characters of data per app instance.

----

Examples
--------

Here are some SmartApps that make use of state. You can find them in the IDE along with the other example SmartApps.

- `Smart Nightlight <https://github.com/SmartThingsCommunity/SmartThingsPublic/blob/master/smartapps/smartthings/smart-nightlight.src/smart-nightlight.groovy>`__ - shows using state to store time information.
- `Laundry Monitor <https://github.com/SmartThingsCommunity/SmartThingsPublic/blob/master/smartapps/smartthings/laundry-monitor.src/laundry-monitor.groovy>`__ - uses state to store boolean state and time information.
- `Good Night <https://github.com/SmartThingsCommunity/SmartThingsPublic/blob/master/smartapps/smartthings/good-night.src/good-night.groovy>`__ - shows using state to store time information, including constructing a Date object from a value stored in state.
