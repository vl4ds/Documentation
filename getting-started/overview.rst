.. _get-started-overview:

Overview
========

SmartThings is the open developer platform for the Internet of Things.

With SmartThings, developers can:

- Create applications that let users connect devices, actions, and external services to create automations.
- Integrate new devices into the SmartThings ecosystem.
- Publish applications and device integrations to the SmartThings catalog.

Developer Highlights
--------------------

SmartThings was built to be developer-friendly. Some of the key developer features:

- A simple programming framework using the Groovy programming language. Don't know Groovy? No worries. If you've programmed before, you'll be able to pick it up easily.
- An architecture that allows developers to control hardware with simple software. Turning a switch on is as easy as ``switch.on()``.
- A web-based IDE for developing SmartThings solutions.
- A simulator for testing your code, *even if you don't have specific devices you are developing for*.
- An active and growing `community <https://community.smartthings.com/>`__ of SmartThings developers.

How it Works
------------

There are two primary ways that developers can create with SmartThings.

SmartApps
`````````

.. code-block:: groovy

    def someoneArrived(evt) {
        lights.on()
        sonos.playText("Welcome home!")
    }

*SmartApps* are small programs that allow users to connect their devices to make their home more intelligent. As the world around us becomes more and more connected, it is the intelligence *between* these devices that makes our world smart. SmartApps allow developers to control hardware with simple software.

SmartApps can typically be summarized by what they do. Some example SmartApps:

- *"Turn the lights off after a certain time when no motion is detected"*
- *"Notify me if a door opens when I'm not home"*
- *"Turn my thermostat down when I leave home"*


SmartThings ships with many SmartThings already available. Almost all automations that you configure with your SmartThings mobile application are SmartApps. If you've set up your lights to come on when motion is detected, or to receive a notification if your door opens when you aren't home, you've used SmartApps.

Device Integrations
```````````````````

Developers can also integrate new devices into the SmartThings ecosystem by creating *Device Type Handlers*. These Groovy programs encapsulate the details of communication between SmartThings and the physical devices. In the SmartApp code example above, we turned the lights on by simply calling ``lights.on()``. The Device Type handler is responsible for physically turning the light on (don't worry about the details of this just yet).

.. code-block:: groovy

    def on() {
    	'zcl on-off on'
    }

Putting It All Together
-----------------------

To help better understand the relationship between SmartApps and Device Type Handlers, we will illustrate a typical use case: *"Turn the lights on when a door opens"*:

<diagram showing the flow as a door is opened, and then the light is turned on>

What's Next?
------------

To start developing with SmartThings, you will need to create a developer account and become familiar with the developer tools. This is covered next in the :ref:`quick-start`.
