Overview
========

The *things* that are integrated with the SmartThings platform are
generally referred to as *devices*. Devices are of a specific
*device type*. A device type can be generic (e.g., a thermostat) or it
can manufacturer and even model specific (e.g., a Honeywell Thermostat).

The SmartThings architecture provides a unique abstraction of devices
from their distinct capabilities and attributes in a way that allows
developers to build applications that are insulated from the specifics
of which device they are using. For example, there are lots of
wirelessly controllable “switches”. A switch is any device that can be
turned On or Off. 

When a SmartApp interacts with the virtual representation of a device,
it knows that the device supports certain actions based on its
capabilities. A device that has the "switch" capability must support
both the "on" and "off" actions. In this way, all switches are the same,
and it doesn't matter to the SmartApp what kind of switch is actually
involved. 

This virtual representation of the device is called a device-type handler.

.. note::

    This layer of abstraction is key to the successful function and flexibility of the SmartThings platform. Architecturally, device-type handlers are the bridge between generic capabilities and the device or protocol specific interface actually used to communicate with the device.

The diagram below depicts where device-type handlers sit in the
SmartThings architecture.

.. figure:: ../img/device-types/smartthings-architecture.png
   :alt: Smart Things Architecture


In the example shown above, the job of the device-type handler (that is
implementing the "switch" capability) is to parse incoming,
protocol-specific status messages from the device and turn them into
normalized "events". It is also responsible for accepting normalized
commands (such as "on" and "off") and turning those into the
protocol-specific commands that can be sent to the device to affect the
desired action.

For example, for a Z-Wave compatible on-off switch, the incoming status
messages used by the device to report an "on" or "off" state are as
shown below:

==============	=================================
Device Command	Protocol-Specific Command Message
==============	=================================
on				command: 2003, payload: FF
off				command: 2003, payload: 00
==============	=================================

Whereas the device status reported to the SmartThings platform for the
device is literally just a simple "on" or "off".

Similarly, when a SmartApp or the mobile app invoked an "on" or "off"
command for a switch device, the command that is sent to the device-type
handler is just that simple: "on" or "off". The device-type handler must
turn that simple command into a protocol-specific message that can be
sent down to the device to affect the desired action.

The table below shows the actual Z-Wave commands that are sent to a
Z-Wave switch by the device-type handler.

==============	=================================
Device Command	Protocol-Specific Command Message

On				2001FF
Off				200100
==============	=================================

Core Concepts
-------------

To understand how device-type handlers work, a few core concepts need to be discussed.

Capabilities
~~~~~~~~~~~~

Capabilities are the interactions that a device allows. We have a
`reference
document <https://graph.api.smartthings.com/ide/doc/capabilities>`__
that lists all available capabilities. 

Device-type handlers may declare that they support one or many capabilities. A device type for a switch may support the "Switch" and "Actuator" capabilities, for example.

Capabilities may have attributes and commands (discussed below). When your device type defines capabilities, you are stating that your device supports the given capability and its associated attributes and commands.

Attributes
~~~~~~~~~~

Attributes represent particular state values for your device. For example, the switch capability defines the attribute "switch", with possible values of "on" and "off". 

Attributes are often used in SmartApps that use a particular device. Continuing the example above, a SmartApp using a device that supports the switch capability can get the value of the switch by invoking the "current<attributeName>" method (``currentSwitch`` in this case).


Commands
~~~~~~~~

Commands are the actions to be taken on your device. If your device type defines capabilities, those capabilities may define commands. A device type may also define custom capabilities.

The device type must implement all the commands supported by the device - whether they are defined by the capability, or defined by the device-type handler itself.

Protocols
---------

SmartThings currently supports both the `Z-Wave <http://en.wikipedia.org/wiki/Z-Wave>`__ and `ZigBee <http://en.wikipedia.org/wiki/ZigBee>`__ wireless protocols. Bluetooth support is planned for our next generation hub. 

Since the device-type handler is responsible for communicating between the device and the SmartThings platform, it is usually necessary to understand and communicate in whatever protocol the device supports. This guide will discuss both Z-Wave and ZibBee protocols at a high level.