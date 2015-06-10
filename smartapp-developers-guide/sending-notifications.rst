Sending Notifications
=====================

SmartApps can send notifications, either as a push notification in the mobile app, or as SMS messages to designated recipients.

In this guide, you will learn:

- How to send push notifications to the mobile app
- How to send SMS notifications

.. contents::

Send Push Notifications
-----------------------

To send a push notification through the SmartThings mobile app, you can use the ``sendPush()`` or ``sendPushMessage()`` methods. 
Both methods simply take the message to display.
``sendPush()`` will display the message in *Hello, Home*; ``sendPushMessage()`` will not. 

A simple example below shows (optionally) sending a push message when a door opens:

.. code-block:: groovy

    preferences {
        section("Which door?") {
            input "door", "capability.contactSensor", required: true, 
                  title: "Which door?"
        }
        section("Send Push Notification?") {
            input "sendPush", "bool", required: false, 
                  title: "Send Push Notification when Opened?"
        }
    }

    def installed() {
        initialize()
    }

    def updated() {
        initialize()
    }

    def initialize() {
        subscribe(door, "contact.open", doorOpenHandler)
    }

    def doorOpenHandler(evt) {
        if (sendPush) {
            sendPush("The ${door.displayName} is open!")
        }
    }

Push notifications will be sent to all users with the SmartThings mobile app installed, for the account the SmartApp is installed into.

Send SMS Notifications
----------------------

In addition to sending push notifications through the SmartThings mobile app, you can also send SMS messages to specified numbers using the ``sendSms()`` and ``sendSmsMessage()`` methods.

Both methods take a phone number (as a string) and a message to send. 
The message can be no longer than 140 characters. ``sendSms()`` will display the message in *Hello, Home*; ``sendSmsMessage()`` will not.

Extending the example above, let's add the ability for a user to (optionally) send an SMS message to a specified number:

.. code-block:: groovy

    preferences {
        section("Which door?") {
            input "door", "capability.contactSensor", required: true, 
                  title: "Which door?"
        }
        section("Send Push Notification?") {
            input "sendPush", "bool", required: false, 
                  title: "Send Push Notification when Opened?"
        }
        section("Send a text message to this number (optional)") {
            input "phone", "phone", required: false
        }
    }

    def installed() {
        initialize()
    }

    def updated() {
        initialize()
    }

    def initialize() {
        subscribe(door, "contact.open", doorOpenHandler)
    }

    def doorOpenHandler(evt) {
        def message = "The ${door.displayName} is open!"
        if (sendPush) {
            sendPush(message)
        }
        if (phone) {
            sendSms(phone, message)
        }
    }

SMS notifications will be sent from the number 844647 ("THINGS").

Examples
--------

Several examples exist in the SmartApp templates that send notifications. Here are a few you can look at to learn more:

- "Notify Me When" sends push or text messages in response to a variety of events.
- "Presence Change Push" and "Presence Change Text" send notifications when people arrive or depart.

Related API Documentation
-------------------------
- :ref:`smartapp_send_push`
- :ref:`smartapp_send_push_message`
- :ref:`smartapp_send_sms`
- :ref:`smartapp_send_sms_message`

