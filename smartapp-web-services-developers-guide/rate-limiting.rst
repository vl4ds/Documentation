.. _web_services_rate_limiting:

Rate Limiting
=============

SmartApps or Device Handler's that expose web services are limited in the number of inbound requests they may receive in a time window.

----

Overview
--------

This is to ensure that no one SmartApp or Device Handler consumes too many resources in the SmartThings cloud.
There are various headers available on every request that provide information about the current rate limit limits for a given installed SmartApp or Device Handler.
These are discussed further below.

SmartApps or Device Handlers that expose web APIs are limited to receiving 250 requests in 60 seconds.

----

Rate Limit Headers
------------------

The SmartThings platform will set three HTTP headers on the response for every inbound API call, so that a client may understand the current rate limiting status:

*X-RateLimit-Limit: 250*
   The rate limit - in this example, the limit is 250 requests.

*X-RateLimit-Current: 1*
   The current count of requests for the given time window. In this example, there has been one request within the current rate limit time window.

*X-RateLimit-TTL: 58*
   The time remaining in the current rate limit window. In this example, there is 58 seconds remaining before the current rate limit window resets.

----

Rate Limit HTTP Status Code
---------------------------

In addition to the three HTTP headers above, when the rate limit has been exceeded, the HTTP status code of 429 will be sent on the response.
