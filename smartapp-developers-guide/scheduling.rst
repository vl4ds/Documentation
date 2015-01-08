Scheduling
==========

SmartApps often have the need to schedule certain actions to take place at a given point in time. For example, an app may want to turn off the lights five minutes after someone leaves. Or maybe an app wants to turn the lights on every day at a certain time.

Broadly speaking, there are a couple different ways we might want to schedule something to happen:

- Do something after a certain time amount from now.
- Do something at certain time in the future.

We'll look at each scenario in detail.

Schedule From Now
-----------------

A SmartApp may want to take some action in a certain amount of time after some event has occurred. Consider a few examples:

- When a door closes, turn a light off after two minutes.
- When everyone leaves, adjust the thermostat after ten minutes.
- If a door opens and is not shut after five minutes, send a notification.

All these scenarios follow a common pattern: when a certain event happens, take some action after a given amount of time. In SmartApps, we accomplish this by using the ``runIn`` method.

**runIn(seconds, method, options)**

Executes the specified method in the specified number of seconds from now:

.. code-block:: groovy

    
    def someEventHandler(evt) {
        // execute handler in five minutes from now
        runIn(60*5, handler)
    }

    def handler() {
        switch.off()
    }


*options*
    Optional. Map of options. Valid options:

    [overwrite: ``true`` or ``false``]

    By default, if a method is scheduled to run in the future, and then another call to runIn with the same method is made, the last one overwrites the previously scheduled method. This is usually preferable. Consider the situation if we have a switch scheduled to turn off after five minutes of a door closing. First, the door closes at 2:50 and we schedule the switch to turn off after five minutes (2:55). Then two minutes later (2:52), the door opens and closes again - another call to runIn will be made to schedule the switch to turn off in five minutes from now (2:57). If we specified ``[overwrite: false]``, we'd now have two schedules to turn off the switch - one at 2:55, and one at 2:57. So, if you do specify ``[overwrite: false]``, be sure to write your handler so that it can handle multiple calls.

.. code-block:: groovy


    def someEventHandler(evt) {
        runIn(300, handler, [overwrite: false])
    }

    def handler() {
        // need to handle multiple calls since overwrite:false specified
    }

.. note::

    It is important to note that you should not rely on the method being called in *exactly* the specified number of seconds. SmartThings will *attempt* to execute the method within a minute of the time specified, but cannot guarantee it. See the :ref:`limitations_best_practices` topic below for more information.

    You should not write your SmartApp expecting second-level granularity. You may find that it is accurate to the second-level in your case, but it cannot be guaranteed. 

    This is especially important for calls to runIn with values less than one minute. 



Schedule at a Certain Time
--------------------------

Some SmartApps may need to schedule certain actions to happen at a specific time and date. This is typically useful for apps that want to set up a recurring schedule (turn the lights of at 11:30 PM, for example).

There are a few different methods available to us to handle these requirements.

**runOnce(dateTime, handlerMethod)**

Executes the handlerMethod once at the specified date and time. The dateTime argument can be either a Date object or an ISO-8601 date string. 

.. code-block:: groovy

    
    // execute handler tomorrow, at the current time
    def someEventHandler(evt) {
        runOnce(new Date() + 1, handler)
    }

    def handler() {
        switch.off()
    }

.. code-block:: groovy

    
    def someEventHandler(evt) {
        // execute handler at 4 PM GMT on October 21, 2015 (e.g., Back to the Future 2 Day!)
        runOnce("2015-10-21T16:00:00.000Z", handler)
    }

    def handler() {
        // do something awesome, like ride a hovercraft
    }



----

**schedule(cronExpression, method)**

Creates a scheduled job that calls the handlerMethod according to the specified cronExpression. 

.. code-block:: groovy

    
    def someEventHandler(evt) {
        // execute handlerMethod every hour on the half hour.
        schedule("0 30 * * * ?", handlerMethod)
    }

    def handlerMethod() {
        ...
    }

Scheduled jobs are limited to running no more often than once per minute.

For information on cron expressions, see `this page <http://quartz-scheduler.org/documentation/quartz-1.x/tutorials/crontrigger>`__.

----

**schedule(dateString, method)**

Creates a scheduled job that calls the handlerMethod according to the specified dateString. This is
typically used when gathering the execution time from the app preferences. 

.. code-block:: groovy

    
    preferences {
        section("Time to run") {
            input "time1", "time"
        }
    }

    ...

    def someEventHandler(evt) {
        schedule(time1, handlerMethod)
    }

    def handlerMethod() {
        ...
    }

----

**schedule(dateTime, method)**

Creates a scheduled job that calls the handlerMethod once per day according to the specified dateTime.

.. code-block:: groovy

    def someEventHandler(evt) {
        // execute handlerMethod every day at this time
        schedule(new Date(), handlerMethod)
    }

    def handlerMethod() {
        ...
    }


Other Scheduling-related Methods
--------------------------------

**canSchedule()**

returns ``true`` if a job can be scheduled, ``false`` otherwise. Only four jobs may be scheduled for the future at any time.

.. code-block:: groovy


    def someEventHandler(evt) {
        runIn(300, someHandlerMethod1)
        runIn(300, someHandlerMethod2)
        runIn(300, someHandlerMethod3)
        runIn(300, someHandlerMethod4)

        // false, since we already have four jobs scheduled 
        canSchedule()
    }

----

**unschedule(nameOfMethod = '')**

Removes the method from the schedule queue, if specified.

.. code-block:: groovy

    // unschedule the someHandlerMethod 
    unschedule("someHandlerMethod")

unschedule can also be called with no arguments to unschedule all jobs.

.. code-block:: groovy

    
    // unschedule all jobs
    unschedule()

.. _limitations_best_practices:

Scheduling Limitations and Best Practices
-----------------------------------------

When using any of the scheduling APIs, it's important to understand some limitations and best practices. These limitations are due in part to the fact that execution occurs in the cloud, and are thus subject to limiting factors like load, network connectivity, etc.

----

**Do not expect second-level granularity in scheduled jobs**

SmartThings will *try* to execute your scheduled job at the specified time, but cannot guarantee it will execute at that exact moment. As a general rule of thumb, you should expect that your job will be called within the minute of scheduled execution. For example, if you schedule a job at 5:30:20 (20 seconds past 5:30) to execute in five minutes, we expect it to be executed at some point in the 5:35 minute. 

When using ``runIn`` with less than one minute from now, your mileage will vary.

----

**Only four jobs may be scheduled at any time**

To prevent any one SmartApp or device-type handler from using too many resources, only four jobs may be scheduled for future execution at any time.

----

**Do not excessively schedule/poll**

While there are some limitations in place to prevent excessive scheduling, it's important to note that excessive polling or scheduling is discouraged. It is one of the items we look for when reviewing community-developed SmartApps or device-type handlers.


Examples
--------

These SmartApps can be viewed in the IDE using the "Browse Templates" button:

- "Once a Day" uses ``schedule`` to turn switches on and off every day at a specified time.
- "Turn It On For 5 Minutes" uses ``runIn`` to to turn a switch off after five minutes.
- "Left It Open" uses ``runIn`` to see if a door has been left open for a specified number of minutes.
- "Medicine Reminder" uses ``schedule`` to check if a medicine door has been opened at a certain time. 







