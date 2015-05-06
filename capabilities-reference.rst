.. _capabilities_taxonomy:

Capabilities Taxonomy
=====================

Capabilities are core to the SmartThings architecture. 
They allow us to abstract specific devices into their underlying capabilities. 


An application interacts with devices based on their capabilities, so once we understand the capabilities that are needed by a SmartApp, and the capabilities that are provided by a device, we can understand which devices (based on the Device's declared capabilities) are eligible for use within a specific SmartApp.

Capabilities themselves are decomposed into both Commands and Attributes.
Commands represent ways in which you can control or actuate the device, whereas Attributes represent state information or properties of the device.

Capabilities are created and maintained by the SmartThings internal development team. 


This page serves as a reference for the supported capabilities.

.. note::

    This document is a work in progress. 
    Many capabilities are not yet fully documented (those that don't have links to more information).
    We are continually working to document all the capabilities.

Quick Reference
---------------

The Capabilities quick reference table below lists all capabilities. The various columns are:

**Name:** 
    The name of the capability that is used by a Device Handler.
**Preferences Reference:**
    The string you would use in a SmartApp to allow a user to select from devices supporting this capability.
**Attributes:** 
    The attributes that the capability defines. If an attribute has a specific set of possible values, they will be listed as bullets underneath the attribute.
**Commands:**
    The commands (and their signatures) that the capability defines.

============================= ====================================== ===================================== ========================
       Name                   Preferences Reference                  Attributes                            Commands
============================= ====================================== ===================================== ========================
:ref:`acceleration-sensor`    capability.accelerationSensor          acceleration
                                                      
                                                                     - "inactive"
                                                      
                                                                     - "active"  
:ref:`actuator`               capability.actuator
:ref:`alarm`                  capability.alarm                       alarm                                 off()                                
                                                      
                                                                     - "strobe"                            strobe()

                                                                     - "siren"                             siren()

                                                                     - "off"                               both()

                                                                     - "both"
:ref:`battery`                capability.battery                     battery
:ref:`beacon`                 capability.beacon                      presence

                                                                     - "present"

                                                                     - "not present"
Button                        capability.button                      button

                                                                     - "held"

                                                                     - "pushed"
Carbon Monoxide Detector      capability.carbonMonoxideDetector      carbonMonoxide
Color Control                 capability.colorControl                hue                                   setHue(number)                                

                                                                     saturation                            setSaturation(number)
 
                                                                     color                                 setColor(color_map)
Configuration                 capability.configuration                                                     configure()
Contact Sensor                capability.contactSensor               contact

                                                                     - "open"

                                                                     - "closed"       
Door Control                  capability.doorControl                 door                                  open()

                                                                     - "unknown"                           close()

                                                                     - "closed"

                                                                     - "open"

                                                                     - "closing"

                                                                     - "opening"        
Energy Meter                  capability.energyMeter                 energy
Illuminance Measurement       capability.illuminanceMeasurement      illumance
Image Capture                 capability.imageCapture                image                                 take()
Indicator                     capability.indicator                   indicatorStatus                       indicatorWhenOn()

                                                                     - "when on"                           indicatorWhenOff()

                                                                     - "never"                             indicatorNever()

                                                                     - "when off"                                          
Location Mode                 capability.locationMode                mode
Lock                          capability.lock                        lock                                  lock()
                                                             
                                                                     - "locked"                            unlock()
                                                                     - "unlocked"                                                                                                                                                                                                                                                       
Lock Codes                    capability.lockCodes                   lock                                  lock()

                                                                     codeReport                            unlock()

                                                                     codeChanged                           updateCodes(json_object)

                                                                                                           setCode(number, string)
   
                                                                                                           deleteCode(number)
   
                                                                                                           requestCode(number)

                                                                                                           reloadAllCodes()                                  
Media Controller              capability.mediaController             activities                            startActivity(string)
                               
                                                                     currentActivity                       getAllActivities()

                                                                                                           getCurrentActivity()
Momentary                     capability.momentary                                                         push()
Motion Sensor                 capability.motionSensor                motion

                                                                     - "active"
                                                                     - "inactive"
Music Player                  capability.musicPlayer                 status                                play()
                      
                                                                     level                                 pause()
  
                                                                     trackDescription                      stop()

                                                                     trackData                             nextTrack()

                                                                     mute                                  playTrack(string)

                                                                     - "muted"                             setLevel(number)
                                                                     - "unmuted"                           
                                                                                                           playText(string)
  
                                                                                                           mute()
 
                                                                                                           previousTrack()
 
                                                                                                           unmute()

                                                                                                           setTrack(string)

                                                                                                           resumeTrack(string)

                                                                                                           restoreTrack(string)
Polling                       capability.polling                                                           poll()
Power Meter                   capability.powerMeter                  power
Presence Sensor               capability.presenceSensor              presence

                                                                     - "present"
                                                                     - "not present"
Refresh                       capability.refresh                                                           refresh()
Relative Humidity Measurement capability.relativeHumidityMeasurement humidity
Relay Switch                  capability.relaySwitch                 switch                                on()

                                                                     - "off"                               off()
                                                                     - "on"                                     
Sensor                        capability.sensor
Signal Strength               capability.signalStrength              lqi

                                                                     rssi

Sleep Sensor                  capability.sleepSensor                 sleeping
                                                                     
                                                                     - "not sleeping"
                                                                     - "sleeping"
Smoke Detector                capability.smokeDetector               smoke

                                                                     - "detected"
                                                                     - "clear"
                                                                     - "tested"
Speech Synthesis              capability.speechSynthesis                                                   speak(string)
Step Sensor                   capability.stepSensor                  steps
                                                                     goal
Switch                        capability.switch                      switch                                on()

                                                                     - "off"                               off()
                                                                     - "on"  
Switch Level                  capability.switchLevel                 level                                 setLevel(number, number)
Temperature Measurement       capability.temperatureMeasurement      temperature
Thermostat                    capability.thermostat                  temperature                           setHeatingSetpoint(number)

                                                                     heatingSetpoint                       setCoolingSetpoint(number)

                                                                     coolingSetpoint                       off()

                                                                     thermostatSetpoint                    heat()

                                                                     thermostatMode                        emergencyHeat()

                                                                     - "auto"                              cool()
                                                                     - "emergency heat"
                                                                     - "heat"                              setThermostatMode(enum)
                                                                     - "off"
                                                                     - "cool"                              fanOn()

                                                                     thermostatFanMode                     fanAuto()

                                                                     - "auto"                              fanCirculate()
                                                                     - "on"
                                                                     - "circulate"                         setThermostatFanMode(enum)

                                                                     thermostatOperatingState              auto()

                                                                     - "heating"
                                                                     - "idle"
                                                                     - "pending cool"
                                                                     - "vent economizer"
                                                                     - "cooling"
                                                                     - "pending heat"
                                                                     - "fan only"   
Thermostat Cooling Setpoint   capability.thermostatCoolingSetpoint   coolingSetpoint                       setCoolingSetpoint(number)
Thermostat Fan Mode           capability.thermostatFanMode           thermostatFanMode                     fanOn()

                                                                     - "on"                                fanAuto()
                                                                     - "auto"
                                                                     - "circulate"                         fanCirculate()

                                                                                                           setThermostatFanMode(enum)
Thermostat Heating Setpoint   capability.thermostatHeatingSetpoint   heatingSetpoint                       setHeatingSetpoint(number)
Thermostat Mode               capability.thermostatMode              thermostatMode                        off()
                                  
                                                                     - "emergency heat"                    heat()
                                                                     - "heat"
                                                                     - "cool"                              emergencyHeat()
                                                                     - "off"
                                                                     - "auto"                              cool()

                                                                                                           auto()

                                                                                                           setThermostatMode(enum)
Thermostat Operating State    capability.thermostatOperatingState    thermostatOperatingState

                                                                     - "idle"
                                                                     - "fan only"
                                                                     - "vent economizer"
                                                                     - "cooling"
                                                                     - "pending heat"
                                                                     - "heating"
                                                                     - "pending cool"
Thermostat Setpoint           capability.thermostatSetpoint          thermostatSetpoint
Three Axis                    capability.threeAxis                   threeAxis
Tone                          capability.tone                                                              beep()
Touch Sensor                  capability.touchSensor                 touch
                                                                     
                                                                     - "touched"
Valve                         capability.valve                       contact                               open()

                                                                     - "closed"                            close()
                                                                     - "open"                                     
Water Sensor                  capability.waterSensor                 water

                                                                     - "dry"
                                                                     - "wet"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
============================= ====================================== ===================================== ========================

.. _acceleration-sensor:

Acceleration Sensor
~~~~~~~~~~~~~~~~~~~

The Acceleration Sensor capability allows for acceleration detection.

Some use cases for SmartApps using this capability would be detecting if a washing machine is vibrating, or if a case has moved (particularly useful for knowing if a weapon case has been moved).

==================== =====================
Capability Name      Preferences Reference
==================== =====================
Acceleration Sensor  capability.accelerationSensor
==================== =====================

**Attributes:**

============ ====== ===============
Attribute    Type   Possible Values
============ ====== ===============
acceleration String ``"active"`` if acceleration is detected.

                    ``"inactive"`` if no acceleration is detected.
============ ====== ===============                    

**Commands:**

None.

**SmartApp Example**

.. code-block:: groovy
    
    // preferences reference
    preferences {
        input "accelerationSensor", "capability.accelerationSensor"
    }

    def installed() {
        // subscribe to active acceleration 
        subscribe(accelerationSensor, "acceleration.active", 
                  accelerationActiveHandler)

        // subscribe to inactive acceleration
        subscribe(accelerationSensor, "acceleration.inactive",
                  accelerationInactiveHandler)

        // subscribe to all acceleration events
        subscribe(accelerationSensor, "acceleration", accelerationBothHandler)
    }



.. _actuator:

Actuator
~~~~~~~~

The Actuator capability is a "tagging" capability. It defines no attributes or commands.

In SmartThings terms, it represents that a Device has commands.

----

.. _alarm:

Alarm
~~~~~

The Alarm capability allows for interacting with devices that serve as alarms.

.. note::

    Z-Wave sometimes uses the term "Alarm" to refer to an important notification.
    The *Alarm* Capability is used in SmartThings to define a device that acts as an Alarm in the traditional sense (e.g., has a siren and such).

+------------------+--------------------------------+
| Capability Name  | SmartApp Preferences Reference |
+==================+================================+
| Alarm            | capability.alarm               |
+------------------+--------------------------------+

**Attributes:**

=========   =========   ===============
Attribute   Type        Possible Values
=========   =========   ===============
alarm       String      ``"strobe"`` if the alarm is strobing.

                        ``"siren"`` if the alarm is sounding the siren.

                        ``"off"`` if the alarm is turned off.

                        ``"both"`` if the alarm is strobing and sounding the alarm.
=========   =========   ===============

**Commands:**

*strobe()*
    Strobe the alarm

*siren()*
    Sound the siren on the alarm.

*both()*
    Strobe and sound the alarm

*off()*
    Turn the alarm (siren and strobe) off

**SmartApp Example:**

.. code-block:: groovy
    
    // preferences reference
    preferences {
        input "alarm", "capability.alarm"
    }

    def installed() {
        // subscribe to alarm strobe
        subscribe(alarm, "alarm.strobe", strobeHandler)

        // subscribe to all alarm events
        subscribe(alarm, "alarm", allAlarmHandler)

        def strobeHandler(evt) {
            log.debug "${evt.value}" // => "strobe"
        }
    }

    def allAlarmHandler(evt) {
        if (evt.value == "strobe") {
            log.debug "alarm strobe"
        } else if (evt.value == "siren") {
            log.debug "alarm siren"
        } else if (evt.value == "both") {
            log.debug "alarm siren and alarm"
        } else if (evt.value == "off") {
            log.debug "alarm turned off"
        } else {
            log.debug "unexpected event: ${evt.value}"
        }
    }

----

.. _battery: 

Battery
~~~~~~~

Defines that the device has a battery.

================ ==============================
Capability Name  SmartApp Preferences Reference
================ ==============================
Alarm            capability.alarm          
================ ==============================

**Attributes:**

========== ======= ===============
Attribute  Type    Possible Values
========== ======= ===============
battery    Number  A number that represents the value of the specified battery.
========== ======= ===============

**Commands:**

None

**SmartApp Example:**

.. code-block:: groovy

    preferences {
        section() {
            input "thebattery", "capability.battery"
        }
    }

    def installed() {
        def batteryValue = thebattery.latestValue("battery")
        log.debug "latest battery value: $batteryValue"

        subscribe(thebattery, "battery", batteryHandler)
    }

    def batteryHandler(evt) {
        log.debug "battery attribute changed to ${evt.value}"
    }

----

.. _beacon: 

Beacon
~~~~~~

================ ==============================
Capability Name  SmartApp Preferences Reference
================ ==============================
Beacon           capability.beacon          
================ ==============================

**Attributes:**

=========== ======= =================
Attribute   Type    Possible Values
=========== ======= =================
presence    String  ``"present"``
                    
                    ``"not present"``
=========== ======= =================

**Commands:**

None.

**SmartApp Example:**

.. code-block:: groovy

    preferences {
        section() {
            input "thebeacon", "capability.beacon"
        }
    }

    def installed() {
        def currBeacon = thebeacon.currentValue("presence")
        log.debug "beacon is currently: $currBeacon"

        subscribe(thebeacon, "presence", beaconHandler)
    }

    def beaconHandler(evt) {
        log.debug "beacon presence is: ${evt.value}"
    }

----