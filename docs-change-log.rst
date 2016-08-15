========================
Documentation Change Log
========================

A log of changes to this documentation.

----

15 August 2016
--------------

`GitHub Release Tag <https://github.com/SmartThingsCommunity/Documentation/releases/tag/15-August-2016>`__

- Make edits to Makefile as a first step in getting generated capabilities documentation integrated into the documentation build.

----

04 August 2016
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

28 July 2016
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
