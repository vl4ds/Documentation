SmartApp Overview
=================

Event Handler SmartApps
-----------------------

Event Handler SmartApps are the most common apps developed by our
community. They allow you to do things in response to device events 
or schedules. A very simple example would be a SmartApp that turns the
lights off when everyone leaves. 

We're confident that if you are familiar with back-end development of web
sites, you will be more than capable of developing SmartApps.

Solution Module SmartApps
-------------------------

These apps exist within the dashboard of the SmartThings app interface,
and are containers for other SmartApps. The idea behind Solution Module
SmartApps is to combine SmartApps that, in the real world, intuitively
go together. One example of this would be the "Home & Family" section of
the dashboard which allows you to see the comings and goings of your
family.

Solution Module SmartApps are currently only built by our internal
team, but we plan to open them up for external development in the future.

Service Manager SmartApps
-------------------------

Service Manager SmartApps are used to connect to LAN or cloud devices,
such as Sonos or Belkin WeMo. They are the connecting glue between the 
unique protocols of your external devices and a device type handler
you'd create for those devices. They discover devices and then continue
to maintain the connection for those devices.

The Service Manager SmartApp must be installed when a user utilizes a
device using LAN or the cloud. For example, there is a Sonos Service
Manager SmartApp, called Sonos (Connect) that is installed when pairing 
with a Sonos device.
