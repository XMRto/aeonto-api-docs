
Introduction
============

XMR.to_ is a service that allows users to pay any bitcoin address
anonymously using Monero_. 
Monero_ is a secure, private, untraceable currency that is open-source
and freely available to all.
XMR.to_ converts moneroj (XMR) provided by the user to bitcoins
(BTC), which are sent to a given bitcoin address.

XMR.to_ provides an API that enables developers to use the service
in programs, apps, scripts etc. This API therefore allows developers
to integrate an option to pay any bitcoin address in their product,
for example, in a Monero wallet service. 
The API is a REST-like API using `JSON` as data format. It is available anonymously
and does not require identification or authentication.
This document describes this API and gives examples of its usage.

.. warning::
    You agree to XMR.to's Terms of Service by accessing this API in any way or form.
    Read the Terms of Service here: https://xmr.to/static/html/tos.html

Overview
--------

In order to use XMR.to_, a user must first create an order over
a certain amount of bitcoins (BTC). Once the order has been created,
XMR.to_ provides the user with the details for the payment in moneroj (XMR).
Once the user has fully paid using Monero, XMR.to_ will create a bitcoin
transaction over the given amount to the given bitcoin address.

When using the API, the general flow of events is similar:

 - get current order parameters to see if XMR.to_ is available and to fetch current
   price, order limits, etc...
 - create a new order by supplying bitcoin destination address and mount
 - check order status to get payment information
 - pay order
 - continue to repeatedly check order status for processing progress

Naming conventions
------------------

API base URL
~~~~~~~~~~~~

The base URL of the API is ``https://xmr.to/api/``, followed by the version identifier (currently ``v2``),
followed by the conversion direction (currently only ``xmr2btc``). Therefore, the complete API base URL
is ``https://xmr.to/api/v2/xmr2btc/``.

.. note::
    The current version of the API is 2.

API endpoint names
~~~~~~~~~~~~~~~~~~

API endpoints are always named ``<noun>_<verb>``. For example, the endpoint to create
a new order is called ``/order_create/``. For example, for API version 1 the complete URL would be
``https://xmr.to/api/v1/xmr2btc/order_create/``.

Field names and values
~~~~~~~~~~~~~~~~~~~~~~

Fields are named using lower-case nouns separated by underscores. Values are given in their default format.
For example, the field ``btc_amount`` gives the amount of an order in bitcoins rather than satoshis.
Data types are not indicated in field names.


Data and formats
----------------

Responses
~~~~~~~~~

API responses are always `JSON`-formatted data.

Return codes and errors
~~~~~~~~~~~~~~~~~~~~~~~

On success, the API returns the requested data in `JSON` format and `HTTP` return code ``200``.

On failure, the API returns an error message formatted in `JSON` and a `HTTP` error code.
The error message is formatted as follows:

.. sourcecode:: javascript

    {
        "error": "XMRTO-ERROR-<number>"
        "error_msg": "<error_error_message_as_string>"
    }

The ``error`` number is unique per message across all endpoints and can be used to reliably identify
errors when they occur. The ``error_msg`` is a human-readable description of the error and should
not be used by a script or program that uses the API. For example, the error below is returned if the user
provided a malformed bitcoin address when creating a new order:

.. sourcecode:: javascript

    {
        "error": "XMRTO-ERROR-002"
        "error_msg": "malformed btc address"
    }

The error messages and `HTTP` return codes specific to the different API endpoint are defined in
the appropriate section.


Various parameters
------------------

Currently, XMR.to_ has a few additional parameters and best practice values that are constant. Therefore, we do not expose them via a dedicated API endpoint. However, for the sake of completeness, we list them here:

+----------------------------------------------+-----------+
| Description                                  | Value     |  
+==============================================+===========+
| Number of BTC confirmations required before  | 144       |
| we purge an order                            |           |
+----------------------------------------------+-----------+
| Number of seconds after which an order times | 900       |
| out if no or only partial payment occurred   |           |
+----------------------------------------------+-----------+
| Number of recommended mixins to use in       | 3         |
| Monero_ payments                             |           |
+----------------------------------------------+-----------+


.. _XMR.to: https://xmr.to
.. _Monero: https://getmonero.org

