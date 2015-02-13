Submitting SmartApps for Publication
====================================

Overview
--------

To submit your SmartApps for consideration for publication to the SmartThings Platform, you 
can create a Publication Request by clicking on the `My Publication Requests <https://graph.api.smartthings.com/ide/submissions>`__ 
tab in the `SmartThings IDE <http://ide.smartthings.com>`__, then clicking on the *New Request* 
button in the upper-right-hand corner of the screen.

Review Guidelines
-----------------

Once submitted, your SmartApp will undergo a review and approval process.  For the greatest likelihood of success, follow these guidelines:

General
~~~~~~~

- Make sure your SmartApp compiles and runs. Do one last check before submitting!
- Do not use offensive, profane, or libelous language.
- No advertising or sponsorships.

Web Services
~~~~~~~~~~~~

- If your SmartApp sends any data from the SmartThings Platform to an external service, include in the description exactly what data is sent to the remote service, how that data will be used, and include a link to the privacy policy of the remote service
- If your SmartApp exposes any Web Service APIs, describe what the APIs will be used for, what data may be accessed by those APIs, and where possible, include a link to the privacy policies of any remote services that may access those APIs.

Devices and Preferences
~~~~~~~~~~~~~~~~~~~~~~~

- Use capabilities, not device wildcards when gathering device inputs from the user.
- Label your SmartApp preferences to match the devices that you are asking for access to.
- Use the *password* input type whenever you are asking a user for a password.

Scheduling
~~~~~~~~~~

- Do not aggressively loop or schedule.
- If using sunrise and sunset, make sure you are following the guidelines in the `Sunset and Sunrise <sunset-and-sunrise>`__ guide.

Style
~~~~~

- Use meaningful variable names.
- Maintain consistent formatting and indentation. We can't review code that we can't easily read. 
