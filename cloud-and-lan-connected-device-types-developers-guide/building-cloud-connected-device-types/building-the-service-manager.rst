Building the Service Manager
============================

The Service Manager's responsibilities are to:

- Authenticate with the 3rd party cloud service
- Device discovery
- Add/Change/Delete device actions
- Handle sending any messages that require the authentication obtained.

We will look at a detailed example of what is outlined above. But first, let's see an example of how what we are trying to
accomplish would look like in the SmartThings application.

Authentication using OAuth
--------------------------

Any service manager that authenticate with a 3rd party via OAuth must themselves have OAuth enabled. This is because ultimately the 3rd party service will call back into the SmartApp and hit the `/oauth/initialize` and `/oauth/callback` endpoints.

End User Experience
~~~~~~~~~~~~~~~~~~~

The experience for the end user will be fairly seamless. They will go
through the following steps (illustrated using the Ecobee Thermostat)

The user selects the Service Manager application from the SmartApps
within the SmartThings app.

Authorization with the third party is the first part of the
configuration process. The user is driven to a page which tells them
about the authorization process and how it will work. They can then
click a link to move forward.

.. figure:: ../../img/device-types/cloud-connected/building-cloud-connected-device-types/click-to-login.png

The user will be driven to a third party site, embedded within the
SmartThings application chrome. They will be required to put in their
username and password for the third party service.

.. figure:: ../../img/device-types/cloud-connected/building-cloud-connected-device-types/ecobee-login.png

The third party server will show what SmartThings will have access to
and give the user the opportunity to accept or decline.

.. figure:: ../../img/device-types/cloud-connected/building-cloud-connected-device-types/authorize-ecobee.png

Upon acceptance, the user will be redirected to another page within the
third party service. This page includes language about the end user
clicking done on the top right of the SmartThings chrome.

.. figure:: ../../img/device-types/cloud-connected/building-cloud-connected-device-types/ecobee-authorization-complete.png

After done is clicked, the user will go back to the initial
configuration screen. They will be able to pick their device to complete the installation.

Implementation
~~~~~~~~~~~~~~

OAuth is the typical industry standard for authentication. The 3rd party service may use something other than OAuth.
In that case, it is up to you to consult their documentation and implement it. The basic concepts will be the same as
it is with OAuth. The following example will walk through what is necessary for OAuth authentication.

There are two endpoints that all service manager SmartApps must define.

.. code-block:: groovy

    mappings {
        path("/oauth/initialize") {action: [GET: "oauthInitUrl"]}
        path("/oauth/callback") {action: [GET: "callback"]}
    }

The `/oauth/initialize` endpoint will be called during initialization and will ultimately be where we forward the user to the 3rd Party service so they can log in.

The `/oauth/callback` endpoint is what the 3rd party service will redirect to once authentication has been verified. Usually this is where the call is made to the 3rd party service to exchange an authorization code for an access token.

The overall idea is that you will create a page that will call out to
the third party API to initiate the authentication. The end result being an access token that we can then use to communicate with the 3rd parties API.

Within your service manager preferences, you create a page for
authorization.

.. code-block:: groovy

    preferences {
        page(name: "Credentials", title: "Sample Authentication", content: "authPage", nextPage: "sampleLoggedInPage", install: false)
        ...
    }

The authPage method will have a couple of responsibilities.

* Create a SmartApp access token sent along to the 3rd party so that they can call back into our smartapp.
* Check to make sure an access token doesn't already exist for this particular 3rd party service.
* Initialize the OAuth flow with the 3rd party service if there is no access token.

Let's take a look at how we would accomplish this.

.. code-block:: groovy

    def authPage() {
        // Check to see if our SmartApp has it's own access token and create one if not.
        if(!state.accessToken) {
            // the createAccessToken() method will store the access token in state.accessToken
            createAccessToken()
        }

        def redirectUrl = "https://graph.api.smartthings.com/oauth/initialize?appId=${app.id}&access_token=${state.accessToken}&apiServerUrl=${getApiServerUrl()}"
        // Check to see if we already have an access token from the 3rd party service.
        if(!state.authToken) {
            return dynamicPage(name: "auth", title: "Login", nextPage: "", uninstall: false) {
                section() {
                    paragraph "tap below to log in to the 3rd party service and authorize SmartThings access"
                    href url: redirectUrl, style: "embedded", required: true, title: "3rd Party product", description: "Click to enter credentials"
                }
            }
        } else {
            // We have the token, so we can just call the 3rd party service to list our devices and select one to install.
        }
    }

There are a few things worth noting here. First, we are using ``state`` to store our tokens. Your specific needs may be different depending on your implementation. To learn more about how ``state`` works and what your options are, visit the :ref:`storing-data` guide.

If we do not have a token from the 3rd party service, we start the OAuth flow by calling the SmartThings initialize endpoint. This is a static endpoint that will store a few bits of information about your SmartApp like the id, and forward the request on to the `/oauth/initalize` endpoint defined in your SmartApp.

Initialize Endpoint
~~~~~~~~~~~~~~~~~~~

Used to initialize the OAuth flow to a 3rd party service. The initialize endpoint will save all of the query params passed to it, but requires three to be present. The SmartApp ID, the SmartApp's access token, and the api server url which is used to determine which server the SmartApp is installed on. The endpoint will then call the mapped `/oauth/initialize` endpoint defined in your SmartApp will all of the query params passed to it.

.. code-block:: html

    https://graph.api.smartthings.com/oauth/initialize

=================== ===========
Required parameters value
=================== ===========
appId               The SmartApp ID
access_token        The SmartApp's access token
apiServerUrl        The URL of the server that the SmartApp is installed on. This information can be retrieved with the ``getApiServerUrl()`` method call.
=================== ===========

**Example:**

.. code-block:: groovy

    def redirectUrl = "https://graph.api.smartthings.com/oauth/initialize?appId=${app.id}&access_token=${state.accessToken}&apiServerUrl=${getApiServerUrl()}"

The initialize endpoint will forward to the `/oauth/initialize` mapping defined in our SmartApp. This method will be responsible for redirecting the user to the 3rd party login page. Here is an example:

.. code-block:: groovy

    def oauthInitUrl() {

        // Generate a random ID to use as a our state value. This value will be used to verify the response we get back from the 3rd party service.
        state.oauthInitState = UUID.randomUUID().toString()

        def oauthParams = [
            response_type: "code",
            scope: "smartRead,smartWrite",
            client_id: appSettings.clientId,
            client_secret: appSettings.clientSecret,
            state: state.oauthInitState,
            redirect_uri: "https://graph.api.smartthings.com/oauth/callback"
        ]

        redirect(location: "${apiEndpoint}/authorize?${toQueryString(oauthParams)}")
    }

    // The toQueryString implementation simply gathers everything in the passed in map and converts them to a string joined with the "&" character.
    String toQueryString(Map m) {
	    return m.collect { k, v -> "${k}=${URLEncoder.encode(v.toString())}" }.sort().join("&")
    }

This method is pretty straight forward. It sets up a request used to present the user with the 3rd party login page. Often the 3rd party service will require information passed along with this request as query params. The actual parameters sent with the request will vary depending on what the 3rd party service is expecting, so consult their api documentation to find specifics.

We are expecting to get an authorization code as a result from this request that we will later exchange for an access token. We will create the access token request in our callback handler as seen below. But for now, lets look at some basic parameters usually associated with authorization code requests.

================= ===========
Common parameters value
================= ===========
response_type     The type of authorization defined by your 3rd party service. Usually `code` or `token`
scope             Defines the scope of the request, i.e. what actions will be performed
client_id         The client ID issued by the 3rd party service when signing up for access to their api. Usually it is best practice to configure this parameter as an app setting in your SmartApp.
client_secret     The client secret issued by the 3rd party service when signing up for access to their api. Usually it is best practice to configure this parameter as an app setting in your SmartApp.
state             Usually the state is not required, but is used to track state across requests. We will use this to validate the response we get back from the 3rd party.
redirect_uri      The uri to be redirected to after the user has successfully authenticated with the 3rd party service. Usually this is a piece of information requested when signing up with the 3rd party service and this param must match what was entered at that time. For SmartApp development, this should always be the static value: ``https://graph.api.smartthings.com/oauth/callback``
================= ===========

Callback Endpoint
~~~~~~~~~~~~~~~~~

The callback endpoint is what the 3rd party service will redirect to after the user has successfully authenticated. For SmartApp development, this should always be the static value: ``https://graph.api.smartthings.com/oauth/callback``. The callback endpoint is typically where the authorization code acquired from the initialize call will be used to request the access token. Let's look at an example.

.. code-block:: groovy

    def callback() {
        log.debug "callback()>> params: $params, params.code ${params.code}"

        def code = params.code
        def oauthState = params.state

        // Validate the response from the 3rd party by making sure oauthState == state.oauthInitState as expected
        if (oauthState == state.oauthInitState){
            def tokenParams = [
                grant_type: "authorization_code",
                code      : code,
                client_id : appSettings.clientId,
                client_secret: appSettings.clientSecret,
                redirect_uri: "https://graph.api.smartthings.com/oauth/callback"
            ]

            // This URL will be defined by your 3rd party in their api documentation
            def tokenUrl = "https://www.someservice.com/home/token?${toQueryString(tokenParams)}"

            httpPost(uri: tokenUrl) { resp ->
                state.refreshToken = resp.data.refresh_token
                state.authToken = resp.data.access_token
            }

            if (state.authToken) {
                // call some method that will render the successfully connected message
                success()
            } else {
                // gracefully handle failures
                fail()
            }

        } else {
            log.error "callback() failed. Validation of state did not match. oauthState != state.oauthInitState"
        }
    }

    // Example success method
    def success() {
	    def message = """
		    <p>Your account is now connected to SmartThings!</p>
		    <p>Click 'Done' to finish setup.</p>
	    """
	    displayMessageAsHtml(message)
    }

    // Example fail method
    def fail() {
        def message = """
            <p>There was an error connecting your account with SmartThings</p>
            <p>Please try again.</p>
        """
        displayMessageAsHtml(message)
    }

    def displayMessageAsHtml(message) {
        def html = """
            <!DOCTYPE html>
            <html>
                <head>
                </head>
                <body>
                    <div>
                        ${message}
                    </div>
                </body>
            </html>
        """
        render contentType: 'text/html', data: html
    }

In this callback we first check to make sure the state returned from the authorization code request matches what we sent as the state. This is how we know that the response is intended for us. If it matches, we set up the params for the access token request. Common params are as follows:

================= ===========
Common parameters value
================= ===========
grant_type        This is the type of grant we are requesting. The 3rd party service will define the expected value.
code              The authorization code we obtained in the previous request
client_id         The same client_id that we used in the previous request, which was issued by the 3rd party service
client_secret     The same client_secret that we used in the previous request, which was issued by the 3rd party service
redirect_uri      The same redirect_uri that we used in the previous request. This will usually be verified by the 3rd party service
================= ===========

We issue a HTTP POST request to get the token. If we receive a success response, we will save the access token that was issued by the 3rd party service along with the refresh token in ``state``.

Once we have acquired the access token, our authentication process is complete. Usually the next step is to display some message to the end user about the success of the operation.

.. important:: ``revokeAccessToken()`` should be called when the SmartApp's access token is no longer required. This is true when a user uninstalls the SmartApp. It is also a good practice to revoke the access token after successful authentication with the 3rd party, unless the token will be used to access other endpoints in your SmartApp.

Refreshing the OAuth Token
~~~~~~~~~~~~~~~~~~~~~~~~~~

OAuth tokens are available for a finite amount of time, so you will
often need to account for this, and if needed, refresh your
access\_token. Above we illustrated how we initiate the request for the access and refresh tokens, and how we saved them in our SmartApp.
If we make a request to the 3rd party service api's and get an "expired token" response, it is up to us to issue a new request to refresh the access token. This is where the refresh token comes into play.

If you run an API request and your access\_token is determined invalid, for example:

.. code-block:: groovy

    if (resp.status == 401 && resp.data.status.code == 14) {
        log.debug "Storing the failed action to try later"
        def action = "actionCurrentlyExecuting"
        log.debug "Refreshing your auth_token!"
        refreshAuthToken()
        // replay initial request from the action variable
        retryInitialRequest(action)
    }

you can use your refresh\_token to get a new access\_token. To do this,
you just need to post to a specified endpoint and handle the response
properly.

.. code-block:: groovy

    private refreshAuthToken() {
        def refreshParams = [
            method: 'POST',
            uri: "https://api.thirdpartysite.com",
            path: "/token",
            query: [grant_type:'refresh_token', code:"${state.sampleRefreshToken}", client_id:XXXXXXX],
        ]
        try{
            def jsonMap
            httpPost(refreshParams) { resp ->
                if(resp.status == 200)
                {
                    jsonMap = resp.data
                    if (resp.data) {
                        state.sampleRefreshToken = resp?.data?.refresh_token
                        state.sampleAccessToken = resp?.data?.access_token
                }
            }
        }
    }

There are some outbound connections in which we are using OAuth to
connect to a third party device cloud (Ecobee, Quirky, Jawbone, etc). In
these cases it is the third party device cloud that issues an OAuth
token to us so that we can call their APIs.

However these same third party device clouds also support webhooks and
subscriptions that allow us to receive notifications when something
changes in their cloud.

In this case and ONLY in this case the SmartApp (service manager) issues
its OWN OAuth token and embeds it in the callback URL as a way to
authenticate the post backs from the external cloud.

Discovery
---------

Identifying Devices in the Third-Party Device Cloud
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The techniques you will use to identify devices in the third party
cloud will vary, because you are interacting with unique third party
APIs which all have unique parameters. Typically you will authenticate
with the third party API using OAuth. Then call an API specific method.
For example, it could be as simple as this:

.. code-block:: groovy

    def deviceListParams = [
        uri: "https://api.thirdpartysite.com",
        path: "/get-devices",
        requestContentType: "application/json",
        query: [token:"XXXX",type:"json" ]

    httpGet(deviceListParams) { resp ->
            //Handle the response here
    }

Creating Child-Devices
~~~~~~~~~~~~~~~~~~~~~~

Within a service manager SmartApp, you create child devices for all your
respective cloud devices.

.. code-block:: groovy

    settings.devices.each {deviceId->
        def device = state.devices.find{it.id==deviceId}
          if (device) {
            def childDevice = addChildDevice("smartthings", "Device Name", deviceId, null, [name: "Device.${deviceId}", label: device.name, completedSetup: true])
      }
    }

Getting Initial Device State
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Upon initial discovery of a device, you need to get the state of your
device from the third party API. This would be the current status of
various attributes of your device. You need to have a method defined in
your Service Manager that is responsible for connecting to the API and
checking for updates. You set this method to be called from a poll
method in your device type, and in this case, it is called immediately
on initialization. Here is a very simple example, which doesn't take
into account error checking for the http request.

.. code-block:: groovy

    def pollParams = [
        uri: "https://api.thirdpartysite.com",
        path: "/device",
        requestContentType: "application/json",
        query: [format:"json",body: jsonRequestBody]

    httpGet(pollParams) { resp ->
        state.devices = resp.data.devices { collector, stat ->
        def dni = [ app.id, stat.identifier ].join('.')
        def data = [
            attribute1: stat.attributeValue,
            attribute2: stat.attribute2Value
        ]
        collector[dni] = [data:data]
        return collector
        }
    }

Handling Adds, Changes, Deletes
-------------------------------

singleInstance Service Manager
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Adding the tag ``singleInstance: true`` to your service manager will ensure only one instance of the service manager can be installed. All child devices will be installed under the single parent service manager. This enforces a one-to-many relationship between the parent Service Manager SmartApp and any child devices.

.. code-block:: groovy
    :emphasize-lines: 9

    definition(
        name: "Ecobee (Connect)",
        namespace: "smartthings",
        author: "SmartThings",
        description: "Connect your Ecobee thermostat to SmartThings.",
        category: "SmartThings Labs",
        iconUrl: "https://s3.amazonaws.com/smartapp-icons/Partner/ecobee.png",
        iconX2Url: "https://s3.amazonaws.com/smartapp-icons/Partner/ecobee@2x.png",
        singleInstance: true)


Implicit Creation of New Child Devices
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When you update your settings in a Service Manager to add additional
devices, the Service Manager needs to respond by adding a new device
in SmartThings.

.. code-block:: groovy

    updated(){
        initialize()
    }

    initialize(){
        settings.devices.each {deviceId ->
            try {
                def existingDevice = getChildDevice(deviceId)
                if(!existingDevice) {
                    def childDevice = addChildDevice("smartthings", "Device Name", deviceId, null, [name: "Device.${deviceId}", label: device.name, completedSetup: true])
                }
            } catch (e) {
                log.error "Error creating device: ${e}"
            }
        }
    }

Implicit Removal of Child Devices
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Similarly when you remove devices within your Service Manager, they
need to be removed from SmartThings.

.. code-block:: groovy

    def delete = getChildDevices().findAll { !settings.devices.contains(it.deviceNetworkId) }

    delete.each {
        deleteChildDevice(it.deviceNetworkId)
    }

Also, when a Service Manager SmartApp is uninstalled, you need to remove
its child devices.

.. code-block:: groovy

    def uninstalled() {
        removeChildDevices(getChildDevices())
    }

    private removeChildDevices(delete) {
        delete.each {
            deleteChildDevice(it.deviceNetworkId)
        }
    }

.. note:: The addChildDevice, getChildDevices, and deleteChildDevice methods are a part of the :ref:`smartapp_ref` API

Changes in Device Name
~~~~~~~~~~~~~~~~~~~~~~

The device name is stored within the device and you need to monitor if
it changes in the third party cloud.

Explicit Delete Actions
~~~~~~~~~~~~~~~~~~~~~~~

When a user manually deletes a device within the Things screen on the
client device, you need to delete the child devices from within the
Service Manager.
