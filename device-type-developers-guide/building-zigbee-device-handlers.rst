Building ZigBee Device Handlers
===============================

.. note::
    There is a new :ref:`zigbee_ref` that covers ZigBee utilities that are used in this page. If you have not glanced at that reference, we strongly recommend you start there.

There are four common ZigBee commands that you will use to integrate
SmartThings with your ZigBee Devices.

Read
----

Read gets the devices current state and is formatted like this:

.. code-block:: groovy

    def refresh() {
        zigbee.readAttribute(0x0B04, 0x050B)
    }

In this example, the device type (from the "CentraLite Switch" device
type) is calling the "refresh" function. It is sending a ZigBee Read
Attribute request via the ``readAttribute()`` method to read the current state (the active power draw). The
cluster we are reading here is Electrical Measurement (0xB04) and
specifically the Active Power Attribute (0x50B).

+-------------------------------+-----------------------------+
| Component                     | Description                 |
+===============================+=============================+
|0x0B04                         | Cluster                     |
+-------------------------------+-----------------------------+
|0x050B                         | Attribute                   |
+-------------------------------+-----------------------------+

Write
-----

Write sets an attribute of a ZigBee device and is formatted like this:

.. code-block:: groovy

    def configure() {
            zigbee.writeAttribute(8, 0x10, 0x21, 0x0014)
        }

In this example (from the "ZigBee Dimmer" device type) we are writing to
an attribute to set the amount of time it takes for a light to fully dim
on and off. Here we are using the Level Control Cluster (8) to write to
the attribute that defines on and off transition time (0x10). The value
we are using is formatted in an Unsigned 16-bit integer (0x21) with the
payload being in 1/10th of a second. In this case the payload ({0014})
translates to 2 seconds. Breaking the payload down we see that the hex value
of 0x0014 equals the decimal value of 20. 20 * 1/10 of a second equals 2 seconds.

.. note::
  The payload in the example above, {0014}, is a hex string. The length of the payload must be two times the length of the data type. For example, if the datatype is 16-bit, then the payload should be 4 hex digits.

+-------------------------------+-----------------------------+
| Component                     | Description                 |
+===============================+=============================+
|8                              |Cluster                      |
+-------------------------------+-----------------------------+
|0x10                           |Attribute Set                |
+-------------------------------+-----------------------------+
|0x21                           |Data Type                    |
+-------------------------------+-----------------------------+
|0x0014                         |Payload                      |
+-------------------------------+-----------------------------+

Command
-------

Command invokes a command on a ZigBee device and is formatted like this:

.. code-block:: groovy

    def on() {
        zigbee.command(0x0006, 0x01)
    }

In this example (from the "ZigBee Dimmer" device type) we are sending a
ZigBee Command to turn the device on. We use the On/Off Cluster (6) and
send the command to turn on (1). This commands has no payload, so we exclude
it from the passed in parameters.

+-------------------------------+-----------------------------+
| Component                     | Description                 |
+===============================+=============================+
|0x0006                         |Cluster                      |
+-------------------------------+-----------------------------+
|0x01                           |Command                      |
+-------------------------------+-----------------------------+

Configure
--------

Configure reporting instructs a device to notify us when an attribute changes and is
formatted like this:

.. code-block:: groovy

    def configure() {
        configureReporting(0x0006, 0x0000, 0x10, 0, 600, null)
    }

In this example (using the "CentraLite Switch" device type), we are configuring
the switch cluster with a minimum reporting interval as 0 seconds, and a reporting
interval of 10 minutes if there is no activity.

+-------------------------------+-----------------------------+
| Component                     | Description                 |
+===============================+=============================+
|0x0006                         |Cluster                      |
+-------------------------------+-----------------------------+
|0x0000                         |Attribute ID                 |
+-------------------------------+-----------------------------+
|0x10                           |Boolean data type            |
+-------------------------------+-----------------------------+
|0                              |Minimum report time          |
+-------------------------------+-----------------------------+
|600                            |Maximum report time          |
+-------------------------------+-----------------------------+
|null                           |Reportable change (discrete) |
+-------------------------------+-----------------------------+

ZigBee Utilities
----------------

In order to work with ZigBee you will need to use the ZigBee Cluster
Library extensively to look up the proper values to send back and forth
to your device. You can download this document
`here <http://www.zigbee.org/download/standards-zigbee-cluster-library/>`__.

There is also a ZigBee utility class covered in the :ref:`zigbee_ref`

Best Practices
--------------

- The use of 'raw ...' commands is deprecated. Instead use the documented methods on the zigbee library. If you need to do something that requires the use of a 'raw' command let us know and we will look at adding it to the zigbee library.
- Do not use sendEvent() in command methods. Sending events should be handled in the parse method.
