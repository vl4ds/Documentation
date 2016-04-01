.. _smartapp_app_touch:

=========
App Touch
=========

There are certain cases where we want to perform some action when the user chooses to do so, by clicking on the *Play* icon next to the SmartApp.

For example, a custom voice notification SmartApp might want to play the message when the user presses play.

----

.. _subscribe_to_app:

Subscribe to ``app``
--------------------

To enable this feature, you simply subscribe to the ``app``:

.. code-block:: groovy

    def initialize() {
        subscribe(app, appHandler)
    }

    def appHandler(evt) {
        log.debug "app event ${evt.name}:${evt.value} received"
    }

Simply subscribing to the event will cause the app to display with a play icon in the mobile application:

.. image:: ../img/smartapps/app-touch.png
    :scale: 60

Your app event handler method can then take the action it needs to in response to the touch event.
