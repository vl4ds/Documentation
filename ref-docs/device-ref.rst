.. _device_ref:

Device
======

.. contents::

----

capabilities
~~~~~~~~~~~~

The List of Capabilities provided by this Device.

**Signature:**
    ``List<Capability> capabilities``

**Returns:**
    `List`_ <:ref:`capability_ref`> - a List of Capabilities supported by this Device.

**Example:**

.. code-block:: grovy

    def supportedCaps = somedevice.capabilites
    supportedCaps.each {cap ->
        log.debug "This device supports the ${cap.name} capability"
    }

----

.. _List: https://docs.oracle.com/javase/7/docs/api/java/util/List.html
.. _String: http://docs.oracle.com/javase/7/docs/api/java/lang/String.html