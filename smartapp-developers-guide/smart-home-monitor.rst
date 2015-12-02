Smart Home Monitor
==================

Smart Home Monitor allows users to harness their devices to provide customizable home security and monitoring.

There are different alarm states that are each associated with different rules for how your home should react:

Arm (Away)
    The home is unoccupied and you want monitoring by all sensors.

Arm (Stay)
    The home is occupied and you want monitoring by select sensors.

Disarm
    You want to disarm all monitoring and alarms.

You can learn more about the Smart Home Monitor `here <https://support.smartthings.com/hc/en-us/articles/205380154-Smart-Home-Monitor>`__.

----

The ``"alarmSystemStatus"`` Location Event
------------------------------------------

The ``"alarmSystemStatus"`` location event is used by Smart Home Monitor to control the alarm states.

The value of the event is one of the following:

============== ===========
Event Value    Description
============== ===========
"away"         The alarm status is in the "Arm (away)" state.
"stay"         The alarm status is in the "Arm (stay)" state.
"off"          The alarm status is in the "Disarm" state.
"unconfigured" Smart Home Monitor has not yet been configured.
============== ===========

----

Get Current Alarm State
-----------------------

You can get the current alarm state of Smart Home Monitor by getting the value of the ``alarmSystemStatus`` on the ``location``:

.. code-block:: groovy

    def curr = location.currentState("alarmSystemStatus")?.value
    log.debug "current alarm state: $curr"

----

Subscribe to Alarm State
------------------------

You can subscribe to changes in Smart Home Monitor's alarm states just as you would subscribe to any other location event:

.. code-block:: groovy

    subscribe(location, "alarmSystemStatus", alarmStatusHandler)

    def alarmStatusHandler(evt) {
        log.debug "alarm status changed to: ${evt.value}"
    }

You can also subscribe to specific alarm states:

.. code-block:: groovy

    subscribe(location, "alarmSystemStatus.off", alarmOffHandler)

    def alarmOffHandler(evt) {
        log.debug "evt value: ${evt.value}"
        log.debug "alarm is off"
    }

----

Set Alarm State
---------------

You can set the alarm state by sending an ``"alarmSystemStatus"`` location event with one of the supported event values.

.. important::

    Changing the alarm status should **only** be done with the user's full awareness.

    Because Smart Home Monitor uses the alarm status to know how to handle security-related incidents in the user's home, it should **never** be done arbitrarily or without the user knowing that the Smart Home Monitor alarm status will change.

An example of changing the alarm state:

.. code-block:: groovy

    sendLocationEvent(name: "alarmSystemStatus", value: "away")
    sendLocationEvent(name: "alarmSystemStatus", value: "stay")
    sendLocationEvent(name: "alarmSystemStatus", value: "off")
