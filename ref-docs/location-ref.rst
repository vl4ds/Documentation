.. _location_ref:

Location
========

A Location represents a user's geo-location, such as "Home" or "office". Locations do not have to have a SmartThings hub, but generally do.

All SmartApps and Device Handlers are injected with a ``location`` property that is the Location into which the SmartApp is installed.

----

.. _location_contact_book_enabled:

getContactBookEnabled()
-----------------------

``true`` if this location has contact book enabled (has contacts), ``false`` otherwise.

**Signature:**
    ``Boolean getContactBookEnabled()``

**Returns:**
    ``true`` if this location has contact book enabled (has contacts), ``false`` otherwise.

----

getCurrentMode()
----------------

The current Mode for the Location.

**Signature:**
    ``Mode getCurrentMode()``

**Returns:**
    :ref:`mode_ref` - The current mode for the Location.

**Example:**

.. code-block:: groovy

    log.debug "location.currentMode: ${location.currentMode}"

----

getId()
-------

The unique internal system identifier for the Location.

**Signature:**
    ``String getId()``

**Returns:**
    `String`_ - the unique internal system identifier for the Location.

**Example:**

.. code-block:: groovy

    log.debug "location.id: ${location.id}"

----

getHubs()
---------

The list of Hubs for this location. Currently only Hub can be installed into a Location, thought this API returns a List to allow for future expandability.

**Signature:**
    ``List<Hub> getHubs()``

**Returns:**
    List <:ref:`hub_ref`> - the Hubs for this Location.

**Example:**

.. code-block:: groovy

    log.debug "Hubs: ${location.hubs*.id}"

----

getLatitude()
-------------

Geographical latitude of the location. Southern latitudes are negative. Requires that location services are enabled in the mobile app.

**Signature:**
    ``BigDecimal getLatitude()``

**Returns:**
    `BigDecimal`_ - the latitude for the Location.

**Example:**

.. code-block:: groovy

    log.debug "location.latitude: ${location.latitude}"

----

getLongitude()
--------------

Geographical longitude of the location. Western longitudes are negative. Requires that location services are enabled in the mobile app.

**Signature:**
    ``BigDecimal getLongitude()``

**Returns:**
    `BigDecimal`_ - the longitude for the Location.

**Example:**

.. code-block:: groovy

    log.debug "location.longitude: ${location.longitude}"

----

getMode()
---------

The current Mode name for the Location.

**Signature:**
    ``String getMode()``

**Returns:**
    `String`_ - the name of the current Mode for the Location.

**Example:**

.. code-block:: groovy

    log.debug "location mode name: ${location.mode}"

----

getModes()
----------

List of Modes for the Location.

**Signature:**
    ``List<Mode> getModes()``

**Returns:**
    `List`_ <:ref:`mode_ref`> - the List of Modes for the Location.

**Example:**

.. code-block:: groovy

    log.debug "Modes for this location: ${location.modes}"

----

getName()
---------

The name of the Location, as assigned by the user.

**Signature:**
    ``String getName()``

**Returns:**
    `String`_ - the name of the Location as assigned by the user.

**Example:**

.. code-block:: groovy

    log.debug "The name of this location is: ${location.name}"

----

.. _location_set_mode:

setMode()
---------

Set the mode for this location.

**Signature:**
    ``void setMode(String mode)``
    ``void setMode(Mode mode)``

**Returns:**
    void

.. warning::

    ``setMode()`` will raise an error if the specified mode does not exist for the location. You should verify the mode exists as in the example below.

**Example:**

.. code-block:: groovy

    def modeToSetTo = "Home"
    if (location.modes?.find {it.name == modeToSetTo}) {
        location.setMode("Home")
    }

----

getTemperatureScale()
---------------------

The temperature scale ("F" for fahrenheit, "C" for celsius) for this location.

**Signature:**
    ``String getTemperatureScale()``

**Returns:**
    `String`_ - the temperature scale set for this location. Either "F" for fahrenheit or "C" for celsius.

**Example:**

.. code-block:: groovy

    def tempScale = location.temperatureScale
    log.debug "Temperature scale for this location is $tempScale"

----

getTimeZone()
-------------

The time zone for the Location. Requires that location services are enabled in the mobile application.

**Signature:**
    ``TimeZone getTimeZone()``

**Returns:**
    `TimeZone`_ - the time zone for the Location.

**Example:**

.. code-block:: groovy

    log.debug "The time zone for this location is: ${location.timeZone}"

----

getZipCode()
------------

The ZIP code for the Location, if in the USA. Requires that location services be enabled in the mobile application.

**Signature:**
    ``String getZipCode()``

**Returns:**
    `String`_ - the ZIP code for the Location.

**Example:**

.. code-block:: groovy

    log.debug "The zip code for this location: ${location.zipCode}"


.. _BigDecimal: http://docs.oracle.com/javase/7/docs/api/java/math/BigDecimal.html
.. _List: https://docs.oracle.com/javase/7/docs/api/java/util/List.html
.. _String: http://docs.oracle.com/javase/7/docs/api/java/lang/String.html
.. _TimeZone: http://docs.oracle.com/javase/7/docs/api/java/util/TimeZone.html
