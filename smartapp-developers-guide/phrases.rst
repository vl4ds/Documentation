=======
Phrases
=======

Phrases, or "Hello, Home", allow actions to happen when a phrase is executed.

In this chapter, you will learn:

- What phrases are
- How to get the available phrases for a location
- How to execute a phrase

Overview
--------

Phrases allow for certain things to happen whenever it executes. SmartThings comes with a few phrases installed:

- Good Morning! - You or the house is waking up
- Good Night! - You or the house is going to sleep
- Goodbye! - You're leaving the house
- I'm Back! - You've returned to the house

Each phrase can be configured to do certain things. For example, when "I'm Back!" executes, you can set the mode to "Home", unlock doors, adjust the thermostat, etc.

Phrases exist for each location in a SmartThings account.

Get Available Phrases
---------------------

You can get the phrases for the location the SmartApp is installed into by accessing the ``helloHome`` object on the ``location``:

.. code-block:: groovy

    def phrases = location.helloHome?.getPhrases()*.label

.. tip::

    If the above code example, with the ``?`` and ``*`` operator looks foreign to you, read on.

    The ``?`` operator allows us to safely avoid a ``NullPointerException`` should ``helloHome`` be null. It's one of Groovy's niceties that allows us to avoid wrapping calls in ``if(someThing != null)`` blocks. Read more about it `here <http://docs.groovy-lang.org/latest/html/documentation/#_safe_navigation_operator>`__.

    The ``*`` operator is called the *spread operator*, and it invokes the specified action (get the label, in the example above) on all items in a collection, and collects the result into a list. Read more about it `here <http://docs.groovy-lang.org/latest/html/documentation/#_spread_operator>`__.


Execute Phrases
---------------

To execute a Hello, Home phrase, you can call the ``execute()`` method on ``helloHome``:

.. code-block:: groovy

    location.helloHome?.execute("Good Night!")

Allowing Users to Select Phrases
--------------------------------

A SmartApp may want to allow a user to execute certain phrases in a SmartApp. Since the phrases for each location will vary, we need to get the available phrases, and use them as options for an ``enum`` input type.

This needs to be done in a dynamic preferences page, since we need to execute some code to populate the available phrases:

.. code-block:: groovy

    preferences {
    	page(name: "selectPhrases")
    }

    def selectPhrases() {
        dynamicPage(name: "selectPhrases", title: "Select Phrase to Execute", install: true, uninstall: true) {

            // get the available phrases
    		def phrases = location.helloHome?.getPhrases()*.label
    		if (phrases) {
                // sort them alphabetically
            	phrases.sort()
    			section("Hello Home Actions") {
    				log.trace phrases
                    // use the phrases as the options for an enum input
                    input "phrase", "enum", title: "Select a phrase to execute", options: phrases
    			}
    		}
        }
    }

You can read more about the ``enum`` input type and dynamic pages :ref:`here <prefs_and_settings>`.

You can then access the selected phrase like so:

.. code-block:: groovy

    def selectedPhrase = settings.phrase

Example
-------

This example simply shows executing a selected phrase when a switch turns on, and another phrase when a switch turns off:

.. code-block:: groovy

    preferences {
    	page(name: "configure")
    }

    def configure() {
        dynamicPage(name: "configure", title: "Configure Switch and Phrase", install: true, uninstall: true) {
    		section("Select your switch") {
    			input "theswitch", "capability.switch",required: true
    		}

    		def phrases = location.helloHome?.getPhrases()*.label
    		if (phrases) {
            	phrases.sort()
    			section("Hello Home Actions") {
    				log.trace phrases
                    input "onPhrase", "enum", title: "Phrase to execute when turned on", options: phrases, required: true
                    input "offPhrase", "enum", title: "Phrase to execute when turned off", options: phrases, required: true
    			}
    		}
        }
    }

    def installed() {
    	log.debug "Installed with settings: ${settings}"
    	initialize()
    }

    def updated() {
    	log.debug "Updated with settings: ${settings}"
    	unsubscribe()
    	initialize()
    }

    def initialize() {
    	subscribe(theswitch, "switch", handler)
        log.debug "selected on phrase $onPhrase"
        log.debug "selected off phrase $offPhrase"
    }

    def handler(evt) {
    	if (evt.value == "on") {
        	log.debug "switch turned on, will execute phrase ${settings.onPhrase}"
        	location.helloHome?.execute(settings.onPhrase)
        } else {
    	    log.debug "switch turned off, will execute phrase ${settings.offPhrase}"
        	location.helloHome?.execute(settings.offPhrase)
        }
    }

Further Reading
---------------

- :ref:`Preferences and Settings Guide <prefs_and_settings>`
