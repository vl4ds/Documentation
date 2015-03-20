Calling Web Services
====================

SmartApps or device handlers may need to make calls to external web services. There are several APIs available to you to handle making these requests.

The various APIs are named for the underlying HTTP method they will use. ``httpGet()`` makes an HTTP GET request, for example.

The following methods are available for making HTTP requests. You can read more about each of them in the `SmartApp reference documentation <https://graph.api.smartthings.com/ide/doc/smartApp>`__.

 - httpDelete()
 - httpGet()
 - httpHead()
 - httpPost()
 - httpPostJson()
 - httpPutJson()

.. note::
    
    The current implementation uses (and exposes) some Groovy libraries for configuring the request information, as well as processing the response. These libraries are not available for general use in the sandbox; you will need to use our http APIs to make requests.

Here's a simple example of making an HTTP GET request:

.. code-block:: groovy

    def params = [
        uri: "http://httpbin.org",
        path: "/get"
    ]

    try {
        httpGet(params) { resp ->
            resp.headers.each {
               log.debug "${it.name} : ${it.value}"
            }
            log.debug "response contentType: ${resp.contentType}"
            log.debug "response data: ${resp.data}"
        }
    } catch (e) {
        log.error "something went wrong: $e"
    }

Configuring the Request
-----------------------

The various APIs for making HTTP requests all accept a map of parameters that define various information about the request. 

The valid parameters to define your request can be found `here <http://groovy.codehaus.org/modules/http-builder/apidocs/groovyx/net/http/HTTPBuilder.RequestConfigDelegate.html#setPropertiesFromMap(java.util.Map)>`__.

Handling the Response
---------------------

The HTTP APIs accept a closure that will be called with the response information from the reqeust.

The closure is passed an instance of a `HttpResponseDecorator <http://groovy.codehaus.org/modules/http-builder/apidocs/groovyx/net/http/HttpResponseDecorator.html>`__. You can inspect this object to get information about the response.

.. note:: 

    Any request is limited to ten seconds. If the request takes longer than that, it will timeout.

When making HTTP requests, consider any error conditions and handle them appropriately via a try/catch block. 

Try it Out
----------

If you're interested in experimenting with the various HTTP APIs, there are a few tools you can use to try out the APIs without signing up for any API keys.

You can use `httpbin.org <http://httpbin.org/>`__ to test making simple requests. The ``httpGet()`` example above uses it.

For testing POST requests, you can use `PostCatcher <http://postcatcher.in/>`__. You can generate a target URL and then inspect the contents of the request. Here's an example using `httpPostJson()`:

.. code-block:: groovy

    def makePostJsonRequest(evt) {      
        def params = [
            uri: "http://postcatcher.in/catchers/<yourUniquePath>",
            body: [
                param1: [subparam1: "subparam 1 value", 
                         subparam2: "subparam2 value"],
                param2: "param2 value"
            ]
        ]
   
        try {
            httpPostJson(params) { resp ->
                resp.headers.each {
                    log.debug "${it.name} : ${it.value}"
                }
                log.debug "response contentType: ${resp.    contentType}"           
            }
        } catch (e) {
            log.debug "something went wrong: $e"
        }
    }

See Also
--------

You can browse some templates in the IDE that use the various HTTP APIs. The Ecobee Service Manager is an example that uses both ``httpGet()`` and ``httpPost()``.

Groovy references:

 - `valid request parameters <http://groovy.codehaus.org/modules/http-builder/apidocs/groovyx/net/http/HTTPBuilder.RequestConfigDelegate.html#setPropertiesFromMap(java.util.Map)>`__
 - `HttpResponseDecorator <http://groovy.codehaus.org/modules/http-builder/apidocs/groovyx/net/http/HttpResponseDecorator.html>`__ 
