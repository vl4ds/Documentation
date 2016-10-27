.. _smartapp-time-methods:

Working with Time
=================

Monitoring the home and triggering events based on what is detected often entails asking the question: "Is it the right time?" and then based on the answer, perform "Do this, or not," actions.
For example, a SmartApp can turn on a room light when a door is opened but only during certain hours, or wake up the house in the morning at different times based on what day of the week it is.

Time methods can be used in a SmartApp to accomplish such automations.
These time methods support a variety of time-related queries such as get the current time or today's date, know the time zone, or find out if a given moment of time is between a preset time-window.

Taking Action Within a Time Window
----------------------------------

A common automation with SmartThings is to turn on a room light when the door is opened between certain hours, and do not turn on the light during other times.
The :ref:`smartapp_timeofdayisbetween` method comes in handy to set up a SmartApp that accomplishes such an automation.

Refer to the SmartApp code below.
First we set up the ``preferences()`` section with ``openCloseSensor``, an open/close sensor that detects when the door is opened, and a ``roomLight`` that controls the switch to the room light.
With the ``fromTime`` and ``toTime`` inputs the user will set up the preferred time-window during which the light should be turn on whenever the door is opened.

.. code-block:: groovy

    preferences {
        section("Select SmartThings") {
            input "openCloseSensor", "capability.contactSensor", title: "Which door?", required: true, multiple: false
            input "roomLight", "capability.switch", title: "Which room light?", required: true, multiple: false
        }
        section("Turn on between what times?") {
            input "fromTime", "time", title: "From", required: true
            input "toTime", "time", title: "To", required: true
        }

    }


Next, we begin watching the door by creating the ``contactHandler`` event handler and have it subscribe to the ``contact.open`` attribute of the ``openCloseSensor`` contact sensor.
This enables the ``contactHandler`` event handler to be sensitive only to the ``open`` event of the contact sensor, i.e, when the door is opened.

.. code-block:: groovy

    def initialize() {
        subscribe(openCloseSensor, "contact.open", contactHandler)
    }


In the ``contactHandler()`` implementation below, we ensure our SmartApp performs the following checks:

- Is the door open? No? Then do nothing (in this particular example we do not care if the door is closed).
- If the door is open, then are we within the time-window? No? Then do nothing.
- If the door is open, *and* we are within the time-window, then turn on the room light.

.. code-block:: groovy

    def contactHandler(evt) {

        // Door is opened. Now check if the current time is within the visiting hours window
        def between = timeOfDayIsBetween(fromTime, toTime, new Date(), location.timeZone)
        if (between) {
            roomLight.on()
        } else {
            roomLight.off()
        }
    }

The ``timeOfDayIsBetween()`` method returns a Boolean ``true`` or ``false`` following the logic in the table below.

============ ============= ============= ==========
fromTime      toTime        new Date()   between
============ ============= ============= ==========
16:04         16:08         16:03:59      false
16:04         16:08         16:04         true
16:04         16:08         16:08:59      true
16:04         16:08         16:09         false
============ ============= ============= ==========

----
