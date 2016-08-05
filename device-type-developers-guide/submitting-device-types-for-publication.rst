Submitting Device Handlers for Publication
==========================================

To submit your Device Handlers for consideration for publication to the SmartThings Platform, you can create a Publication Request by clicking on the `My Publication Requests <https://graph.api.smartthings.com/ide/submissions>`__  tab in the `SmartThings IDE <http://ide.smartthings.com>`__, then clicking on the *New Request*  button in the upper-right-hand corner of the screen.

----

Review Guidelines
-----------------

Once submitted, your Device Handler will undergo a review and approval process.
For greatest liklihood of approval, review and ensure your code follows the :doc:`../code-review-guidelines`.

Reasons for Rejection
^^^^^^^^^^^^^^^^^^^^^

- The Device Handler adds minor addition or change that may be changed with a core product or UX change in a future update.
- SmartThings is already developing a first-party integration and will not accept a Device Handler for this device.
- The Device Handler should actually be a SmartApp instead, because it's actuating or changing a device.
- Suggested change does not fit our philosophy.
- No discovery mechanism is provided. For LAN-Connected devices, a `Service Manager SmartApp <http://docs.smartthings.com/en/latest/cloud-and-lan-connected-device-types-developers-guide/understanding-the-service-manage-device-handler-design-pattern.html>`_ should serve to discover and create the device.
- Multiple community submissions exist and we’re rolling several improvements together, so this specific one is being rejected.
