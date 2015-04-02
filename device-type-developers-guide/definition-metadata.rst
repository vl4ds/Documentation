Definition
==========

The definition metadata defines core information about your device handler. The initial values are set from the values entered when creating your device handler.

Example definition metadata:

.. code-block:: groovy

    metadata {
        definition(name: "test device", namespace: "yournamespace", author: "your name") {

            capability "Alarm"
            capability "battery"

            attribute "customAttribute", "string"

            command "customCommand"

            fingerprint profileId: "0104", inClusters: "0000,0003,0006",
                        outClusters: "0019"
        }

        ...
    }

The definition method takes a map of parameters, and a closure.

The supported parameters are:

*name*
    The name of the device handler
*namespace*
    The namespace for this device handler. This should be your github user name. This is used when looking up device handlers by name to ensure the correct one is found, even if someone else has used the same name. 
*author*
    The author of this device handler.

The closure defines the capabilities, attributes, commands, and fingerprint information for your device handler.

Capabilities
------------

To define that your defice supports a capability, simply call the ``capability`` method in the closure passed to ``definition``. 

The argument to the ``capability`` method is the Capability name.

.. code-block:: groovy

    capability "Actuator"
    capability "Power Meter"
    capability "Refresh"
    capability "Switch"


Attributes
----------

If you need to define a custom attribute for your device handler, call the ``attribute`` method in the closure passed to the ``definition`` method:

**attribute(String attributeName, String attributeType, List possibleValues = null)**

*attributeName*
    Name of the attribute

*attributeType*
    Type of the attribute. Available types are "string", "number", and "enum"

*possibleValues*
    Optional. The possible values for this attribute. Only valid with the "enum" attributeType.

.. code-block:: groovy

    // String attribute with name "someName"
    attribute "someName", "string"

    // enum attribute with possible values "light" and "dark"
    attribute "someOtherName", "enum", ["light", "dark"]



Commands
--------

To define a custom command for your device handler, call the ``command`` method in the closure passed to the ``definition`` method:

**command(String commandName, List parameterTypes = [])**

*commandName*
    The name of the command. You must also define a method in your device handler with the same name.
*parameterTypes*
    Optional. An ordered list of the parameter types for the command method, if needed. 

.. code-block:: groovy

    // command name "myCommand" with no parameters
    command "myCommand"

    // comand name myCommandWithParams that takes a string and a number parameter
    command "myCommandWithParams", ["string", "number"]

    ...

    // each command specified in the definition must have a corresponding method

    def myCommand() {
        // handle command
    }

    // this command takes parameters as defined in the definition
    def myCommandWithParams(stringParam, numberParam) {
        // handle command
    }


Fingerprinting
--------------

When trying to connect your device to the SmartThings hub, we need a way to identify and join a particular device to the hub. This process is known as a "join" process, or "fingerprinting".

The fingerprinting process is dependent on the type of device you are
looking to pair. SmartThings attempts to match devices coming in based on
the input and output clusters a device uses, as well as a profileId
(for ZigBee) or deviceId (for Z-Wave). Basically, by determining what
capabilities your device has, SmartThings determines what your device is.

ZigBee Fingerprinting
~~~~~~~~~~~~~~~~~~~~~

For ZigBee devices, the main profileIds you will need to use are

-  HA: Home Automation (0104)
-  SEP: Smart Energy Profile
-  ZLL: ZigBee Light Link (C05E)

The input and output clusters are defined specifically by your device
and should be available via the device's documentation.

An example of a ZigBee fingerprint definition:

.. code-block:: groovy

        fingerprint profileId: "C05E", inClusters: "0000,0003,0004,0005,0006,0008,0300,1000", outClusters: "0019"


Z-Wave Fingerprinting
~~~~~~~~~~~~~~~~~~~~~

For Z-Wave devices, the fingerprint should include the deviceId of the 
device and the command classes it supports in the inClusters list. The 
easiest way to find these values is by adding the actual device to 
SmartThings and looking for the *Raw Description* in its details view in 
the SmartThings developer tools. The device class ID is the four-digit 
hexadecimal number (eg. 0x1001) and the command classes are the two-digit 
hexadecimal numbers. So if the raw description is ::

    0 0 0x1104 0 0 0 8 0x26 0x2B 0x2C 0x27 0x73 0x70 0x86 0x72

The fingerprint will be

.. code-block:: groovy

    fingerprint deviceId:"0x1104", inClusters:"0x26, 0x2B, 0x2C, 0x27, 0x73, 0x70, 0x86, 0x72"

If the raw description has two lists of command classes separated by a 
single digit 'count' number, the second list is the outClusters. So for 
the raw description ::

    0 0 0x2001 0 8 0x30 0x71 0x72 0x86 0x85 0x84 0x80 0x70 1 0x20

The fingerprint will be 

.. code-block:: groovy

    fingerprint deviceId:"0x2001", inClusters:"0x30, 0x71, 0x72, 0x86, 0x85, 0x84, 0x80, 0x70", outClusters: "0x20"

Note that the fingerprint clusters lists are comma separated while the raw 
description is not.

The order of the inClusters and outClusters lists is not important. A 
device will match to the *longest* fingerprint for which it matches the 
deviceId and supports all of the clusters – it can have more than the 
fingerprint and still match.
