.. _zigbee_ref:

ZigBee Reference
================

.. note::
    As of now, any time the ZigBee library logs a message, an error will be seen. This is a known issue and will be fixed with the next deploy.

The ZigBee library contains many shorthands and conveniences for developing ZigBee device type handlers.

ZigBee devices have fingerprints that define what the device is when it joins a ZigBee network.
Currently you define the expected fingerprint for a device in the device type handler metadata block as part of the definition. An example would look like this:

.. code-block:: groovy

    metadata {
        // Automatically generated. Make future change here.
        definition (name: "SmartPower Outlet", namespace: "smartthings", author: "SmartThings") {
            capability "Actuator"
            capability "Switch"
            capability "Power Meter"
            capability "Configuration"
            capability "Refresh"
            capability "Sensor"

        // indicates that device keeps track of heartbeat (in state.heartbeat)
        attribute "heartbeat", "string"

        fingerprint profileId: "0104", inClusters: "0000,0003,0004,0005,0006,0B04,0B05", outClusters: "0019", manufacturer: "CentraLite",  model: "3200", deviceJoinName: "Outlet"
        fingerprint profileId: "0104", inClusters: "0000,0003,0004,0005,0006,0B04,0B05", outClusters: "0019", manufacturer: "CentraLite",  model: "3200-Sgb", deviceJoinName: "Outlet"
        fingerprint profileId: "0104", inClusters: "0000,0003,0004,0005,0006,0B04,0B05", outClusters: "0019", manufacturer: "CentraLite",  model: "4257050-RZHAC", deviceJoinName: "Outlet"
        fingerprint profileId: "0104", inClusters: "0000,0003,0004,0005,0006,0B04,0B05", outClusters: "0019"
    }

If the fingerprint declaration contains a value for both the ``manufacturer`` and ``model`` attributes, you have the option to add the ``deviceJoinName`` attribute.

===================== =========== ==========
Fingerprint Attribute Type        Value
===================== =========== ==========
deviceJoinName        ``String``  Overrides the device type name when pairing. This allows the developer to customize the device name while joining a ZigBee device.
===================== =========== ==========

----

Parse Methods
-------------

zigbee.getEvent()
~~~~~~~~~~~~~~~~~

The ``getEvent()`` method will try to parse ZigBee Clusters into a map whose key/value pairs can be directly handled by the ``sendEvent()`` method.

**Signature:**
    .. code-block:: groovy

        Map(String:String) zigbee.getEvent(String description)

**Parameters:**
    - **description**: The description value passed to the parse method from the device.

**Return Values**:
    *Map: [name: <event name>, value: <event value>]*

**Example**

.. code-block:: groovy

    def parse(String description) {
        def result = zigbee.getEvent(description)

        if(result) {
            sendEvent(result)
        } else {
            // zigbee.getEvent was unable to parse description. Description must be parsed manually.
        }
    }

The ``zigbee.getEvent()`` method can parse the following event types with work being done to for additional event types:

================== =================
Event Type         Cluster value
================== =================
switch             0x0006
level              0x0008
power              0x0702 and 0x0B04
colorTemperature   0x0300
================== =================

.. note::
    Only color temperature can be parsed. Full color control is in the works.

.. note::
    For the power event type, the value can be reported in mW, W, or kW. This means that it is up to the developer to make adjustments to the value before calling sendEvent so it will be displayed correctly.

Low Level Commands
------------------

zigbee.command()
~~~~~~~~~~~~~~~~~~~~

Send a Cluster specific Command.

**Signature:**
    .. code-block:: groovy

        zigbee.command(Integer Cluster, Integer Command, [String... payload])

**Parameters:**
    - **Cluster**: The Cluster ID
    - **Command**: The Command ID
    - **payload** (optional): Zero or more arguments required by the Command. Each argument should be passed as an ASCII hex string in little endian format of the appropriate width for the data type. For example, to pass the value 5 for a UINT24 (24-bit unsigned integer) you would pass “050000”.

**Examples:**
    - Send *Move To Level* Command to *Level Control* Cluster.
        .. code-block:: groovy

            zigbee.command(0x0008, 0x04, "FE", "0500")

        Where *Level* equals ``0xFE`` (full on) and *Transition Time* equals ``0x0005`` (5 seconds)

    - Send *Off* Command to *On/Off* Cluster.
        .. code-block:: groovy

            zigbee.command(0x0006, 0x00)

----

zigbee.readAttribute()
~~~~~~~~~~~~~~~~~~~~~~

Read the current attribute value of the specified Cluster.

**Signature:**
    .. code-block:: groovy

        zigbee.readAttribute(Integer Cluster, Integer attributeId)

**Parameters:**
    - **Cluster**: The Cluster ID to read from
    - **attributeId**: The ID of the attribute to read

**Example:**
    - Read *CurrentLevel* attribute of the *Level Control* Cluster.
        .. code-block:: groovy

            zigbee.readAttribute(0x0008, 0x0000)

----

zigbee.writeAttribute()
~~~~~~~~~~~~~~~~~~~~~~~

Write the attribute value of the specified Cluster.

**Signature:**
    .. code-block:: groovy

        zigbee.writeAttribute(Integer Cluster, Integer attributeId, Integer dataType, value)

**Parameters:**
    - **Cluster**: The Cluster ID to write
    - **attributeId**: The attribute ID to write
    - **dataType**: The data type ID of the attribute as specified in the `ZigBee Cluster library <http://www.zigbee.org/download/standards-zigbee-cluster-library/>`__
    - **value**: The Integer value to write for data types of *boolean*, *unsigned int*, *signed int*, *general data*, and *enumerations*. Other data types are not currently supported but will be added in the future. Let us know if you need a data type that is not currently supported.

**Example**:
    - Write the value 0x12AB to a unsigned 16-bit integer attribute
        .. code-block:: groovy

            zigbee.writeAttribute(0x0008, 0x0010, 0x21, 0x12AB)

----

zigbee.configureReporting()
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Configure a ZigBee device's reporting properties. Refer to the *Configure Reporting* Command in the `ZigBee Cluster Library <http://www.zigbee.org/download/standards-zigbee-cluster-library/>`__ for more information.

**Signature:**

.. code-block:: groovy

    zigbee.configureReporting(Integer Cluster,
        Integer attributeId, Integer dataType,
        Integer minReportTime, Integer MaxReportTime,
        [Integer reportableChange])

**Parameters:**
    - **Cluster**: The Cluster ID of the requested report
    - **attributeId**: The attribute ID for the requested report
    - **dataType**: The two byte ZigBee type value for the requested report
    - **minReportTime**: Minimum number of seconds between reports
    - **maxReportTime**: Maximum number of seconds between reports
    - **reportableChange** (optional): Amount of change needed to trigger a report. Required for analog data types. Discrete data types should always provide *null* for this value.

**Examples:**
    - Discrete data type
        .. code-block:: groovy

            zigbee.configureReporting(0x0006, 0x0000, 0x10, 0, 600, null)

    - Analog data type
        .. code-block:: groovy

            zigbee.configureReporting(0x0008, 0x0000, 0x20, 1, 3600, 0x01)

----

ZigBee Capabilities
-------------------

The following table outlines the Commands necessary to both configure and get updated information from ZigBee devices that support the capabilities outlined below.

.. note::
    All methods outlined in the table need the ``zigbee.`` prefix

============= ============================================================= ============================== ==============
Capability    Configure                                                     Refresh                        Notes
============= ============================================================= ============================== ==============
Battery       configureReporting(0x0001, 0x0020, 0x20, 30, 21600, 0x01)
Color Temp    configureReporting(0x0300, 0x0007, 0x21, 1, 3600, 0x10)       readAttribute(0x0300, 0x0007)  For devices that support the Color Control Cluster (0x0300)
Level         configureReporting(0x0008, 0x0000, 0x20, 1, 3600, 0x01)       readAttribute(0x0008, 0x0000)
Power         configureReporting(0x0702, 0x0400, 0x2A, 1, 600, 0x05)        readAttribute(0x0704, 0x0400)  For devices that support the Metering Cluster (0x0704)
Power         configureReporting(0x0B04, 0x050B, 0x29, 1, 600, 0x0005)      readAttribute(0x0B04, 0x050B)  For devices that support the Electrical Measurement Cluster (0x0B04)
Switch        configureReporting(0x0006, 0x0000, 0x10, 0, 600, null)        readAttribute(0x0006, 0x0000)
Temperature   configureReporting(0x0402, 0x0000, 0x29, 30, 3600, 0x0064)
============= ============================================================= ============================== ==============

**Examples:**

- Get the latest *level* value from a dimmer switch. From the table above, we find the *level* capability and look at the **Refresh** column to find the correct Command to execute.

    .. code-block:: groovy

        readAttribute(0x0008, 0x0000)

- Configure the *level* capability for a dimmer type switch. The configure reporting Command from the table above for *level* configures the device for a min reporting interval of 5 seconds, a reporting interval of 1 hour (3600 s) if there has been no activity, and a min level change of 01.

    .. code-block:: groovy

        configureReporting(0x0008, 0x0000, 0x20, 1, 3600, 0x01)

The following utility methods are available as capability based Commands.

zigbee.on()
~~~~~~~~~~~

Sends the *on* Command, ``0x01``, to the *on/off* Cluster, ``0x0006``

**Signature:**

.. code-block:: groovy

    zigbee.on()

----

zigbee.off()
~~~~~~~~~~~~

Sends the *off* Command, ``0x00``, to the *onoff* Cluster, ``0x0006``

**Signature:**

.. code-block:: groovy

    zigbee.off()

----

zigbee.setLevel()
~~~~~~~~~~~~~~~~~

Sends the *level* Command, ``0x04``, to the level control Cluster, ``0x0008`` with the passed in rate.

**Signature:**

.. code-block:: groovy

    zigbee.setLevel(Integer level, Integer rate)

**Parameters:**
    - **level**: A value between 0-100 inclusive.
    - **rate**: Time in tenths of a second. E.g. ``rate = 100 //max value`` translates to 10 seconds.

----

zigbee.setColorTemperature()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Sets the color to the specified temperature value in K.

**Signature:**

.. code-block:: groovy

    zigbee.setColorTemperature(Integer value)

**Parameters:**
    - **value**: The temperature value to set the color to in K. Usually greater than *2700*

ZigBee Helper Commands
----------------------

zigbee.parseDescriptionAsMap()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Parses a device description into a map that contains data and capabilities.

**Signature:**

.. code-block:: groovy

    zigbee.parseDescriptionAsMap(String description)

**Parameters:**
    - **description**: The description string from the device

----

zigbee.convertToHexString()
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Convert the given value to a hex string of given width

**Signature:**

.. code-block:: groovy

    zigbee.convertToHexString(Integer value, Integer width)

**Parameters:**
    - **value**: Integer value to be converted
    - **width**: the minimum width of the hex string. Default value is 2

**Examples:**

.. code-block:: groovy

    zigbee.convertToHexString(10, 2) //result: 0A
    zigbee.convertToHexString(10, 4) //result: 000A

----

zigbee.convertHexToInt()
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Convert the given hex value to an Integer

**Signature:**

.. code-block:: groovy

    zigbee.convertHexToInt(String value)

**Parameters:**
    - **value**: The hex value to be converted to an Integer

**Example:**

.. code-block:: groovy

    zigbee.convertHexToInt("0001") //result: 1
    zigbee.convertHexToInt("000F") //result: 15
    def myInt = zigbee.convertHexToInt("12AB") // result = 4779. The result of calling Integer.parseInt()
        assert myInt == 4779 //true
        assert myInt == 0x12AB //also true

----

zigbee.hexNotEqual()
~~~~~~~~~~~~~~~~~~~~

Returns true if the compared hex values are not equal.

**Signature:**

.. code-block:: groovy

    zigbee.hexNotEqual(String hex1, String hex2)

**Parameters:**
    - **hex1**: Hex value to compare
    - **hex2**: Hex value to compare against first value
