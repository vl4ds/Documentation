.. _smartapp_as_web_service_part_2:

SmartThings Web Services Tutorial - Part 2
==========================================

In `Part 1 <./tutorial-part1.html>`__ of this tutorial, you learned how to create a simple Web Services SmartApp, and install it in the IDE simulator, and make web requests to it.

In part 2, we'll build a simple web application that will integrate with SmartThings and the WebServices SmartApp we created in part 1.

In part 2 of this tutorial, you will learn:

- How to get the API token.
- How to discover the endpoints of a Web Services SmartApp.
- How to make calls to the Web Services SmartApp.

.. contents::

Overview
--------

In part 2 of this tutorial, we will build a simple Sinatra application that will make calls to the Web Services SmartApp we built in Part 1.

If you're not familiar with Sinatra, you are encouraged to try it out. It's not strictly necessary, however, as the application will simply make web requests to get the API token, and get the endpoint. 

.. note::

    If you just want to see the manual steps to make requests to get the access token, discover endpoints, and start making calls, you can see the :ref:`appendix_just_the_urls`.

----

Prerequisites
-------------

.. tip::

    Before starting this tutorial, it's a good idea to uninstall the Web Services SmartApp from the IDE simulator if still running. Also, change the OAuth client and secret by editing the SmartApp (Click the *App Settings* button, expand the OAuth section, and use the links to generate a new ID and secret. Note these, since they will be used later).

Aside from completing Part 1 of this tutorial, you should have Ruby and Sinatra installed.

Visit the `Ruby <http://ruby-lang.org>`__ website to install Ruby, and the `Sinatra Getting Started Page <http://www.sinatrarb.com/intro.html>`__ website for information about installing Sinatra.

----

Step 1 - Bootstrap the Sinatra App
----------------------------------

Create a new directory for the Sinatra app, and change directories to it:

.. code-block:: bash

    mkdir web-app-tutorial
    cd web-app-tutorial

In your favorite text editor*, create a new file called ``server.rb`` and paste the following into it, and save it. Replace the ``client_id`` and ``api_key`` with the OAuth Client ID and OAuth Client Secret of your Web Services SmartApp.

\*(*If your favorite text editor is vim or emacs, then our hats off to you. We're impressed - maybe even a bit intimidated. If your favorite editor is notepad, well... we're not as impressed, or intimidated. :@)*)

.. code-block:: ruby

    require 'bundler/setup'
    require 'sinatra'
    require 'oauth2'
    require 'json'
    require "net/http"
    require "uri"

    # Our client ID and secret, used to get the access token
    client_id = 'YOUR_CLIENT_ID'
    api_key = 'YOUR_CLIENT_SECRET'

    # This is the URI that will be called with our access 
    # code after we authenticate with our SmartThings account
    redirect_uri = 'http://localhost:4567/oauth/callback'

    # This is the URI we will use to get the endpoints once we've received our token
    endpoints_uri = 'https://graph.api.smartthings.com/api/smartapps/endpoints'

    # just store the token globally
    # This is a HORRIBLE idea in a real application, of course.
    # But, it works for our example
    thetoken = ''

    options = {
      site: 'https://graph.api.smartthings.com',
      authorize_url: '/oauth/authorize',
      token_url: '/oauth/token'
    }

    # use the OAuth2 module to handle OAuth flow
    client = OAuth2::Client.new(client_id, api_key, options)

    # handle requests to the application root
    get '/' do
      %(<a href="/authorize">Connect with SmartThings</a>)
    end

    # handle requests to /authorize URL
    get '/authorize' do
        'Not Implemented!'
    end

    # hanlde requests to /oauth/callback URL. We 
    # will tell SmartThings to call this URL with our 
    # authorization code once we've authenticated.
    get '/oauth/callback' do
        'Not Implemented!'
    end

    # handle requests to the /getSwitch URL. This is where
    # we will make requests to get information about the configured
    # switch.
    get '/getswitch' do
        'Not Implemented!'
    end

Create your Gemfile - open a new file in your editor, paste the contents below in, and save it as ``Gemfile``.

.. code-block:: ruby

    source 'https://rubygems.org'

    gem 'sinatra'
    gem 'oauth2'
    gem 'json'

We'll use bundler to install our app. If you don't have it, you can learn how to get started `here <http://bundler.io/>`__.

Back at the command line, run bundle:

.. code-block:: bash

    bundle install

Now, run the app on your local machine::

    ruby server.rb

Visit `http://localhost:4567 <http://localhost:4567>`__. You should see a pretty boring web page with a link to "Connect with SmartThings".

We're using the `OAuth2 module <https://github.com/intridea/oauth2>`__ to handle the OAuth2 flow. We create a new Client, using the ``client_id`` and ``api_key``. We also configure it with the ``options`` data structure that defines the information about the SmartThings OAuth endpoint.

We've handled the root URL to simply display a link that points to the ``/authorize`` URL of our server. We'll fill that in next.

----

Step 2 - Get an Authorization Code
----------------------------------

When the user clicks on the "Connect with SmartThings" link, we need to get our OAuth authorization code. 

To do this, the user will need to authenticate with SmartThings, and authorize the devices this application can work with. Once that has been done, The user will be directed back to a specified ``redirect_url``, with the OAuth authorization code. This will be used (along with the Client ID and secret), to get the access token.

Replace the ``/authorize`` route with the following:

.. code-block:: ruby

    get '/authorize' do
      # Use the OAuth2 module to get the authorize URL.
      # After we authenticate with SmartThings, we will be redirected to the 
      # redirect_uri, including our access code used to get the token
      url = client.auth_code.authorize_url(redirect_uri: redirect_uri, scope: 'app')
      redirect url
    end

Kill the server if it's running (CTRL+C), and start it up again using ``ruby server.rb``.

Visit `http://localhost:4567 <http://localhost:4567>`__ again, and click the "Connect with SmartThings" link.

This should prompt you to authenticate with your SmartThings account (if you are not already logged in), and bring you to a page where you must authorize this application. It should look something like this:

.. figure:: ../img/smartapps/web-services/preferences.png

Click the Authorize button, and you will be redirected back your server.

You'll notice that we haven't implemented handling this URL yet, so we see "Not Implemented!". 

But you can also see there is a URL parameter named "code" on the URL:

.. figure:: ../img/smartapps/web-services/code.png

This is our OAuth authorization code, and we'll use it to get our API token.

----

Step 3 - Get an Access Token
----------------------------

Now that we've received our OAuth authorization code, we can use it to obtain the API token we need to make requests to our Web Services SmartApp.

Replace the ``/oauth/callback`` route with the following:

.. code-block:: ruby

    get '/oauth/callback' do
      # The callback is called with a "code" URL parameter
      # This is the code we can use to get our access token
      code = params[:code]

      # Use the code to get the token.
      response = client.auth_code.get_token(code, redirect_uri: redirect_uri, scope: 'app')
      
      # now that we have the access token, we can use it to get the endpoints
      thetoken = response.token

      redirect '/getswitch'
    end

We first retrieve the access code from the parameters. We use this to get the token using the OAuth2 module. As noted, we're just storing the token in a local variable. This is not what you would do in a reall web application of course, since they should be stateless. But, for the purposes of this tutorial, it will suffice.

We then redirect to the ``/getswitch`` URL of our server. This is where we will retrieve the endpoint to call, and get the status of the configured switch.

Restart your server, and try it out. Once authorized, you should be redirected to the ``/getswitch`` URL. We'll start implementing that next.

----

Step 4 - Discover Endpoints
---------------------------

Now that we have the OAuth token, we can use it to discover the endpoint of our WebServices SmartApp.

Replace the ``/getswitch`` route with the following:

.. code-block:: ruby

    get '/getswitch' do
      # If we get to this URL without having gotten the access token
      # redirect back to root to go through authorization
      if thetoken.empty?
        redirect '/'
      end

      # make a request to the SmartThins endpoint URI, using the token,
      # to get our endpoints
      url = URI.parse(endpoints_uri)
      req = Net::HTTP::Get.new(url.request_uri)

      # we set a HTTP header of "Authorization: Bearer <API Token>"
      req['Authorization'] = 'Bearer ' + thetoken

      http = Net::HTTP.new(url.host, url.port)
      http.use_ssl = (url.scheme == "https")

      response = http.request(req)
      json = JSON.parse(response.body)

      # debug statement
      puts json

      # get the endpoint from the JSON:
      endpoint = json[0]['url']

      '<h3>JSON Response</h3><br/>' + json.to_s + '<h3>Endpoint</h3><br/>' + endpoint 
    end

The above code simply makes a GET request to the SmartThings API endpoints service at ``https://graph.api.smartthings.com/api/smartapps/endpoints``, setting the ``"Authorization"`` HTTP header with the API token.

The response is JSON that contains (among other things), the endpoint of our SmartApp. For this step, we just display the JSON response and endpoint in the page.

By now, you know the drill. Restart your server, refresh the page, and click the link (you'll have to reauthorize). You should then see the JSON response and endpoint displayed on your page.

----

Step 5 - Make API Calls
-----------------------

Now that we have our token and endpoint, we can (gasp!) make API calls to our SmartApp!

As you may have guessed by the URL path, we're just going to display the name of the switch, and it's current status (on or off).

Remove the line at the end of the ``getswitch`` route handler that outputs the response HTML, and add the following:

.. code-block:: ruby

  # now we can build a URL to our WebServices SmartApp
  # we will make a GET request to get information about the switch
  switchUrl = 'https://graph.api.smartthings.com' + endpoint + '/switch?access_token=' + thetoken
  
  # debug
  puts "SWITCH ENDPOINT: " + switchUrl
  
  getSwitchURL = URI.parse(switchUrl)
  getSwitchReq = Net::HTTP::Get.new(getSwitchURL.request_uri)
  
  getSwitchHttp = Net::HTTP.new(url.host, url.port)
  getSwitchHttp.use_ssl = true
  
  switchStatus = getSwitchHttp.request(getSwitchReq)
  switchJson = JSON.parse(switchStatus.body)

  # debugging statements
  puts 'RESPONSE BODY: ' + switchStatus.body
  puts 'SWITCH JSON: ' + switchJson.to_s

  # Just print out the switch name and value 
  switchJson['name'][0] + ' is ' + switchJson['value'][0]

The above code uses the endpoint for our SmartApp to build a URL, and then makes a GET request to the ``/switch`` endpoint. It simply displays the name of the configured switch, and its value.

.. note::

    Note that we used the ``access_token`` URL parameter to specify the API key this time, instead of the ``"Authorization"`` HTTP header. This is just to illustrate that you can use both methods of passing the API key.

Restart your server and try it out. You should see status of your configured switch displayed!

----

Summary
-------

In part 2 of this tutorial, we learned how an external application can work with SmartThings by getting an access token, discover endpoints, and make API calls to a WebServices SmartApp.

You are encouraged to explore further with this sample, including making different API calls to turn the configured switch on or off.

----

.. _appendix_just_the_urls:

Appendix - Just the URLs, Please
--------------------------------

If you want to quickly test getting access to a Web Services SmartApp, without creating an external application, you can use your web browser to make requests to get the API token and endpoint. Most of these steps will not be visible to the end user, but can be useful for testing, or just for reference so you can build your own app.

Here are the steps:

Get the OAuth Authorization Code
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In your web browser, paste in the following URL, replacing the CLIENT_ID with your OAuth Client ID::

    https://graph.api.smartthings.com/oauth/authorize?response_type=code&client_id=CLIENT_ID&scope=app&redirect_uri=https%3A%2F%2Fgraph.api.smartthings.com%2Foauth%2Fcallback

Once authenticated, you will be asked to authorize the external application to access your SmartThings. Select some devices to authorize, and click *Authorize*.

This will redirect you to a page that doesn't exist - but that's ok! The important part is the OAuth authorization code, which is the "code" parameter on the URL. Grab this code, and note it somewhere. We'll use it to get our API token.

Get the API token
~~~~~~~~~~~~~~~~~

Using the code you just received, and our client ID and secret, we can get our access token. Paste the following into your web browser's address bar, replacing CLIENT_ID, CLIENT_SECRET, and CODE with the appropriate values::

    https://graph.api.smartthings.com/oauth/token?grant_type=authorization_code&client_id=CLIENT_ID&client_secret=CLIENT_SECRET&code=CODE&redirect_uri=https%3A%2F%2Fgraph.api.smartthings.com%2Foauth%2Fcallback&scope=app

This should return JSON like the following, from which you can get the ``access_token``:

.. code::

  {
    "access_token": "43373fd2871641379ce8b35a9165e803",
    "expires_in": 1576799999,
    "token_type": "bearer"
  }

Discover the Endpoint URL
~~~~~~~~~~~~~~~~~~~~~~~~~

You can get the endpoint URL for your SmartApp by making a request to the SmartThings endpoints service, specifying your access token.

In your web browser, paste the following into your address bar, replacing ACCESS_TOKEN with the access token you retrieved above.

.. code::

    https://graph.api.smartthings.com/api/smartapps/endpoints?access_token=ACCESS_TOKEN

That should return JSON that contains information about the OAuth client, as well as the endpoint for the SmartApp:

.. code:: 

    [
      {
      "oauthClient": {
        "clientId": "myclient",
        "authorizedGrantTypes": "authorization_code"
      },
      "url": "/api/smartapps/installations/8a2aa0cd3df1a718013df1ca2e3f000c"
      }
    ]

Make API Calls
~~~~~~~~~~~~~~

Now that you have the access token and the endpoint URL, you can make web requests to your SmartApp endpoint using whatever tool you prefer.

Just make sure to preface ``http://graph.api.smartthings.com`` to the beginning of the URL returned above, and any endpoints your SmartApp exposes (e.g., ``/switch``) to the end of the URL.

You can either specify your access token via the ``access_token`` URL parameter as above, or (preferably) use the Authorization header (``"Authorization: Bearer <API TOKEN>"``).
