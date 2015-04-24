.. _attribute_ref:

Attribute
=========

An Attribute represents specific information about the state of a device. For example, the "Temperature Measurement" capability has an attribute named "temperature" that represents the temperature data.

You will typically interact with Attributes values directly, using the ``current<Attribute Name>`` method on a :ref:`device_ref` instance. 

You can get an instance of an Attribute from a Device by referring to the Attribute name directly, though it is not currently particularly useful to do so :)

.. code-block:: groovy

    preferences {
        section() {
            input "mytemp", "capability.temperatureMeasurement"
        }
        ...
        def tempAttribute = mytemp.temperature
        ...
    }

You can view the available attributes for all Capabilities in our :ref:`capabilities_taxonomy`.

.. contents::

----

name
~~~~

The name of the Attribute.

**Signature:**
    ``String name``

**Returns:**
    `String`_ - the name of this attribute

**Example:**

.. code-block:: groovy
    
    preferences {
        section() {
            input "myswitch", "capability.switch"
        }
    }
    ...
    // switch capability has an attribute named "switch"
    def switchAttr = myswitch.switch
    log.debug "switch attribute name: ${switchAttr.name}"
    ...

----

.. _String: http://docs.oracle.com/javase/7/docs/api/java/lang/String.html