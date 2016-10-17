========================
Documentation Change Log
========================

A log of changes to this documentation.

----

October 17 2016
---------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/17-October-2016>`__

- Documentation for :ref:`beta asynchronous HTTP APIs <async_http_guide>`
- Typo fixes and other copy edits

----


October 13 2016
---------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/13-October-2016>`__

- Moved rate limiting documentation into its own :doc:`guide <ratelimits/index>`
- Typo fixes and other copy edits

----

October 11 2016
---------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/11-October-2016>`__

- Documented :ref:`sms_rate_limits`
- Fixed typos

----


October 06 2016
---------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/06-October-2016>`__

- Added instructions for creating a simple code example when :ref:`creating a developer support ticket <developer_support_form>`.
- Added :ref:`documentation <custom_remove_button>` for specifying a custom Remove button for preferences.

----

October 05 2016
---------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/05-October-2016>`__

- Added documentation for :ref:`passing data to schedule handler methods <scheduling_passing_data>`.
- Added :ref:`best practices <review_guidelines_parent_child>` for parent-child relationships.
- Updated the :doc:`README` with pull request guidelines.
- Added scheduling APIs to the :ref:`device_handler_ref` reference documentation (including all ``runEvery*`` APIs, which are now supported in Device Handlers).
- Fixed broken cron tutorial link the :ref:`smartapp-scheduling` guide.
- Added note to the :ref:`first SmartApp tutorial <first-smartapp-tutorial>` and :ref:`editor_and_simulator` that the simulator is inconsistent with the mobile application.

----

September 23 2016
-----------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/23-September-2016>`__

- Added link to the Z-Wave public spec on the following Z-Wave pages: :ref:`Building Z-Wave Device Handlers <zwave-device-handlers>` and :ref:`Z-Wave Primer <zwave-primer>`
- Updated the :ref:`Color Control <color_control>` capability to correctly reflect the capability definition.
- Updated Jinja template to add some more features for the ongoing generated capability documentation project.
- Fixed minor grammatical errors.

----

September 14 2016
-----------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/14-September-2016>`__

- Update to the :ref:`State and Atomic State documentation <storing-data>` to reorganize, clarify, and expand content.

----

September 09 2016
-----------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/09-September-2016>`__

- Removed Occupancy capability
- Fixed :ref:`smartapp_unschedule` docs to clarify that a specific handler method name can be passed to ``unschedule()``.

September 02 2016 (3)
---------------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/02-September-2016-03>`__

- Fixing RTD build

----

September 02 2016 (2)
---------------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/02-September-2016-02>`__

- Fixing RTD build

----

September 02 2016
-----------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/02-September-2016>`__

- Typos and spelling fixes
- Added more around the generated capabilities documentation framework
- Added :ref:`web_services_smartapps_troubleshooting` document to the SmartApp Web Services guide
- Fixed :ref:`color_control` example code in the capabilities reference

----

August 17 2016
--------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/17-August-2016>`__

- Fix :ref:`documentation <smartapp_subscribe_to_command>` for ``subscribeToCommand()`` (only takes a Device argument, not a list of Devices)
- Typos and spelling fixes

----

August 16 2016
--------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/16-August-2016>`__

- :ref:`Documentation <logging_exceptions>` for the ability to pass a ``Throwable`` to logging methods to get more logging details about the exception shown in the logs.

----

August 15 2016
--------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/15-August-2016>`__

- Make edits to Makefile as a first step in getting generated capabilities documentation integrated into the documentation build.

----

August 04 2016
--------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/04-August-2016>`__

- Added :ref:`zigbee_parse_zone_status` documentation
- Added documentation for :ref:`zigbee_additional_zigbee_classes`
- Clarified :ref:`smartapp_find_child_app_by_name` API documentation
- Added :doc:`documentation <device-type-developers-guide/other-available-apis>` to Device Handler Guide for other useful APIs available to Device Handlers, including Scheduling, HTTP Requests, and State.
- Fixed documentation for :ref:`Event.dateValue <event_date_value>` to indicate that it returns ``null`` if date cannot be parsed
- Various fixes for reStructuredText formatting and legal syntax warnings
- Moved this documentation change log to top of navigation

----

July 28 2016
------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/28-July-2016>`__

- Document the new :ref:`hideWhenEmpty <prefs_hide_when_empty>` preferences option.

----

July 25 2016
------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/25-July-2016>`__

- Add a strong warning to the :ref:`State documentation <storing-data>` to emphasize the importance of never mixing ``atomicState`` and ``state`` in the same SmartApp.

----

July 21 2016
------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/21-July-2016>`__

- :ref:`Documented <webservices_smartapp_enable_oauth>` the new redirect URI field on OAuth SmartApps

----

July 07 2016
------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/07-July-2016>`__

- Added documentation for :ref:`working with collections in State and Atomic State <state_using_collections>`
- Added documentation for :doc:`ref-docs/app-state-ref`
- Added documentation for :doc:`ref-docs/installed-smart-app-wrapper-ref`
- Added :ref:`clarification <run_api_smartapp_simulator>` that the callable URL for Web Services SmartApps will vary by installed location
- Updated :ref:`developer_discussions` with the new developer call schedule

----

June 23 2016
------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/23-June-2016>`__

- Splitting the Music Player `capability <http://docs.smartthings.com/en/latest/capabilities-reference.html>`_ into three capabilities
    - Audio Notification
    - Music Player
    - Tracking Music Player

----

June 17 2016
------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/17-June-2016>`__

- Adding `WOL (Wake On Lan) documentation <http://docs.smartthings.com/en/latest/cloud-and-lan-connected-device-types-developers-guide/building-lan-connected-device-types/building-the-device-type.html#wake-on-lan-wol>`_

----

June 13 2016
------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/13-June-2016>`__

- Adding :doc:`Code Review Guidelines and Best Practices <code-review-guidelines>` for SmartApps and Device Handlers.

----

June 9 2016
-----------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/09-June-2016>`__

- Fix spelling of "capability" in :ref:`attribute_ref` docs
- Fix capitalization of "localIP" in :ref:`hub_ref` docs
- Document the :ref:`developer_support_form` form
- Document :doc:`Device Handler Preferences <device-type-developers-guide/device-preferences>`
- Document :ref:`device-specific preference inputs <device_specific_inputs>`
- Clarify :doc:`tools-and-ide/github-integration` only available in the US

----

May 27 2016
-----------

- Add ``additionalParams`` argument for ZigBee library. :doc:`Docs <ref-docs/zigbee-ref>` | `GitHub PR <https://github.com/SmartThingsCommunity/Documentation/pull/315>`__

----

May 23 2016
-----------

- Updated and expanded Device Handler tiles docs. :doc:`Docs <device-type-developers-guide/tiles-metadata>`  | `GitHub PR <https://github.com/SmartThingsCommunity/Documentation/pull/314>`__.
