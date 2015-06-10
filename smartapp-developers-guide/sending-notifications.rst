Sending Notifications
=====================

SmartApps can send notifications, either as a push notification in the mobile app, or as SMS messages to designated recipients.

In this guide, you will learn:

- How to send push notifications to the mobile app
- How to send SMS notifications
- How to display messages in *Hello, Home*

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

Send Both Push and SMS Notifications
------------------------------------

The ``sendNotification()`` method allows you to send both push and/or SMS messages, in one convenient method call. It can also optionally display the message in *Hello, Home*.

``sendNotification()`` takes a message parameter, and a map of options that control how the message should be sent, if the message should be displayed in *Hello, Home*, and a phone number to send an SMS to (if specified):

.. code-block:: groovy

    // sends a push notification, and displays it in Hello Home
    sendNotification("test notification - no params")

    // same as above, but explicitly specifies the push method (default is push)
    sendNotification("test notification - push", [method: "push"])

    // sends an SMS notification, and displays it in Hello Home
    sendNotification("test notification - sms", [method: "phone", phone: "1234567890"])

    // Sends a push and SMS message, and displays it in Hello Home
    sendNotification("test notification - both", [method: "both", phone: "1234567890"])

    // Sends a push message, and does not display it in Hello Home
    sendNotification("test notification - no event", [event: false])

Only Display Message in Hello, Home
-----------------------------------

Use the ``sendNotificationEvent()`` method to display a message in *Hello, Home*, without sending a push notification or SMS message:

.. code-block:: groovy

    sendNotificationEvent("Your home talks!")


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
- :ref:`smartapp_send_notification`
- :ref:`smartapp_send_notification_event`

