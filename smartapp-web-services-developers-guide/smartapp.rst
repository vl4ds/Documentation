.. _webservices_smartapp:

The SmartApp
============

A Web Services SmartApp exposes endpoints that third parties can make REST calls to.
It can then do anything a normal SmartApp can do - get device status, actuate devices, etc. - and send a response back to the calling client.

----

Enable OAuth
------------

For a SmartApp to expose endpoints that can receive REST calls from a third party, OAuth must be enabled.

OAuth can be enabled for a SmartApp via the *App Settings* page.
In the *OAuth* section, check the box to enable OAuth.
A client ID and secret will be generated for this SmartApp.
These will be used as part of the OAuth flow to obtain an access token for this SmartApp.

You can also set the Client Display Name and Client Display Link.
These will be used on the SmartThings Authorization page to inform the user who is requesting access to their devices.

.. image:: ../img/smartapps/web-services/oauth-settings.png

----

Preferences
-----------

Part of the :ref:`Authorization Flow <webservices_authorization>` that installs the SmartApp requires the user to authorize specific devices that the third party can interact with.
The types of devices that may be authorized for Web Services SmartApps are controlled through the SmartApp's preferences.

The intent of the Authorization page is to simply allow the user to authorize specific devices.
This can be accomplished in one of two ways:

- Specify a simple, single page that allows the user to select from a set of devices, or
- Specify a specific preferences page to be used by the Authorization web page.

An example of a simple, single page that will allow the user to select from a set of devices:

.. code-block:: groovy

    preferences {
        section("Control these switches...") {
            input "switches", "capability.switch"
        }
        section("Control these motion sensors...") {
            input "motion", "capability.motionSensor"
        }
    }

Here is an example that specifies a specific page to be used during authorization, using the ``oauthPage`` option to ``preferences``:

.. code-block:: groovy

    preferences(oauthPage: "deviceAuthorization") {
        // deviceAuthorization page is simply the devices to authorize
        page(name: "deviceAuthorization", title: "", nextPage: "instructionPage",
             install: false, uninstall: true) {
            section("Select Devices to Authorize") {
                input "switches", "capability.switch", title: "Switches:"
                input "motions", "capability.motionSensor", title: "Motion Sensors:"
            }

        }

        page(name: "instructionPage", title: "Device Discovery", install: true) {
            section() {
                paragraph "Some other information"
            }
        }
    }

If you require additional, non-device preferences inputs, you can use dynamic pages.
The ``oauthPage`` must be a static (non-dynamic) page, and be the first page displayed:

.. code-block:: groovy

    preferences(oauthPage: "deviceAuthorization") {
        // deviceAuthorization page is simply the devices to authorize
        page(name: "deviceAuthorization", title: "", nextPage: "otherPage",
             install: false, uninstall: true) {
            section("Select Devices to Authorize") {
                input "switches", "capability.switch", title: "Switches:"
                input "motions", "capability.motionSensor", title: "Motion Sensors:"
            }

        }

        page(name: "otherPage")
    }

    def otherPage() {
        dynamicPage(name: "otherPage", title: "Other Page", install: true) {
            section("Other Inputs") {
                input "sometext", "text"
                input "sometime", "time"
            }
        }
    }

----

Mapping Endpoints
-----------------

To expose a callable endpoint in your SmartApp, use ``mappings``.
Specify the various endpoints using ``path``, and specify the supported HTTP methods (``GET``, ``PUT``, ``POST``, and ``DELETE``).
Each action specified is associated with the name of a method that will handle the request.

.. code-block:: groovy

    mappings {
        path("/foo") {
            action: [
                GET: "getFoo",
                PUT: "putFoo",
                POST: "postFoo",
                DELETE: "deleteFoo"
            ]
        }
        path("/bar") {
            action: [
                GET: "getBar"
            ]
        }
    }

    def getFoo() {}
    def putFoo() {}
    def postFoo() {}
    def deleteFoo() {}
    def getBar() {}

There is no limit to the number of endpoints a SmartApp exposes, but the path level is restricted to four levels deep (i.e., /level1/level2/level3/level4).

You can specify variable URL path parameters using the ``:`` prefix in the path:

.. code-block:: groovy

    mappings {
        path("/foo/:param1/:param2") {
            action: [GET: "getFoo"]
        }
    }

----

.. _webservices_smartapp_request_handling:

Request Handling
----------------

When a request is made to one of the SmartApp's endpoints, its associated request handler method will be called.

Every request handler method has available to it a ``request`` object that represents information about the request, and a ``params`` object that contains information about the request parameters.

Path variables
``````````````

Any path variables you defined in the ``path`` are available via the injected ``params`` object:

.. code-block:: groovy

    mappings {
        path("/switches/:command") {
            action: [PUT: "updateSwitches"]
        }
    }

    def updateSwitches() {
        def cmd = params.command
        log.debug "command: $cmd"
    }

Query parameters
````````````````

URL query parameters sent on the request are available via the ``params`` object:

.. code-block:: groovy

    def someHandler() {
        // this endpoint can accept the "foo" query parameter
        def fooParam = params.foo
        log.debug "foo parameter: $foo"
    }

Request body parameters
```````````````````````

SmartThings supports JSON or XML request body parameters.
They can be accessed via ``reqeust.JSON`` and ``request.XML``:

.. code-block:: groovy

    // json on request: '{"foo": "bar"}'
    def someJSONHandler() {
        def fooJSON = request.JSON?.foo
        log.debug "foo json: $fooJSON"
    }

    // xml on request: '<foo>bar</foo>'
    def someXMLHandler() {
        def fooXML = request.XML?.foo
        log.debug "foo xml: $fooXML"
    }

.. tip::

    Use the ``?`` (safe navigation operator) to avoid a ``NullPointerException`` if the request JSON or XML is null (in case the request did not send JSON or XML).

The JSON available on the ``request`` will be the result of calling ``new JsonSlurper().parseText()``. You can learn more about working with JSON in Groovy `here <http://www.groovy-lang.org/json.html>`__.

Similarly, the XML on ``request`` is the result of calling ``new XmlSlurper().parseText()``. Learn more about working with XML in Groovy `here <http://www.groovy-lang.org/processing-xml.html>`__.

----

Response Handling
-----------------

SmartApp endpoints can send response data to the client.

The simplest case is returning a map, which will be serialized to JSON and sent to the client:

.. code-block:: groovy

    def someHandler() {
        return [foo: "XXXX", bar: "YYYY"]
    }

The example above would return a ``200`` response with a ``application/json`` response body of:

.. code::

    {
        "foo":"XXXX",
        "bar":"YYYY"
    }


The handler can also use the :ref:`smartapp_render` method for  more fine-grained control over the response:

.. code-block:: groovy

    def someHandler() {
        def html = """
    	<!DOCTYPE html>
        <html>
            <head><title>Some Title</title></head>
            <body><p>Testing</p></body>
        </html>"""

        render contentType: "text/html", data: html
    }

If not specified, the ``contentType`` will be "application/json", and the ``status`` will be ``200``.

.. note::
    ``PUT`` request handlers return a ``204 - No Content`` response, and cannot be overriden.
    
----

Error Handling
--------------

If your endpoint needs to send an error response, use the :ref:`smartapp_http_error` method:

.. code-block:: groovy

    def someHandler() {
        def foo = request.JSON?.foo

        if (!foo) {
            httpError(400, "Foo parameter required")
        }
    }

A ``SmartAppException`` will be thrown, and a response will be sent to the client with the specified HTTP code.
The body of the response will be ``application/json``, and look like this:

.. code::

    {
        "error":true,
        "type":"SmartAppException",
        "message":"your error message"
    }


You should send appropriate error codes and messages for any errors.

For any uncaught exceptions that occur in the SmartApp, a generic ``500`` response will be sent to the client.
It will look something like this:

.. code::

    {
        "error": true,
        "type": "java.lang.NullPointerException",
        "message": "An unexpected error has occurred."
    }

The type and message will vary depending upon the specific SmartApp error.
