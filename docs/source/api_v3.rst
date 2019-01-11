Version 3
=========

This is the current version of the API. Active from January 2019 onwards.

.. note::
    API version 3 is the current version.

Querying order parameters
-------------------------

Reference
~~~~~~~~~

API endpoint: https://aeon.to/api/v3/aeon2btc/order_parameter_query/

The order parameter endpoint supplies information about whether new orders can be created.
In this case, this endpoint provides the current price, order limits, etc. for newly created orders.

.. note::
    It is possible to query the status of existing orders even if the order parameter
    endpoint reports `not available`.

**Request**

Issue a `GET` request to query current order parameters.

**Response**

On success (`HTTP` code ``200``), the request returns the following `JSON` data:

.. sourcecode:: javascript

    {
        "lower_limit": <lower_order_limit_in_btc_as_float>,
        "price": <price_of_1_aeon_in_btc_as_offered_by_service_as_float>,
        "upper_limit": <upper_order_limit_in_btc_as_float>,
        "zero_conf_enabled": <true_if_zero_conf_is_enabled_as_boolean>,
        "zero_conf_max_amount": <up_to_this_amount_zero_conf_is_possible_as_float>
    }

Fields should be self-explanatory.

On failure, one of the following errors is returned:

=========   ===================     =================================    ================
HTTP code   AEON.to error code       Error message/reason                 Solution
=========   ===================     =================================    ================
``503``     ``AEONTO-ERROR-001``     internal services not available      try again later
``503``     ``AEONTO-ERROR-007``     third party service not available    try again later
``503``     ``AEONTO-ERROR-008``     insufficient funds available         try again later
``403``     ``AEONTO-ERROR-012``     too many requests                    try less often
=========   ===================     =================================    ================


Example
~~~~~~~

**Request**

.. sourcecode:: bash

    curl https://aeon.to/api/v3/aeon2btc/order_parameter_query/

or

.. sourcecode:: bash

    http https://aeon.to/api/v3/aeon2btc/order_parameter_query/

**Response**

.. sourcecode:: javascript

    {
        "price": 0.017666,
        "upper_limit": 20.0,
        "lower_limit": 0.002,
        "zero_conf_enabled": true,
        "zero_conf_max_amount": 0.1
    }


Rate limitation
~~~~~~~~~~~~~~~

Too many requests in a short time will result in a rate limitation of the endpoint.

It is possible to request the endpoint up to **3 times per second**.


Creating a new order
--------------------

Reference
~~~~~~~~~

API endpoint: https://aeon.to/api/v3/aeon2btc/order_create/

The order creation endpoint allows to create a new order at the current price.
The user has to supply a bitcoin destination address and amount to create the order.

.. note::
    Please use the ``order_check_price`` API endpoint if you only want to check the price for a specific Bitcoin amount.

**Request**

Issue a `POST` request to create a new order supplying the following parameters:

.. sourcecode:: javascript

    {
        "btc_amount": <requested_amount_in_btc_as_float>,
        "btc_dest_address": <requested_destination_address_as_string>
    }

.. note::
    Make sure that ``btc_amount`` amount is inside the possible limits for an order.
    These limits can be queried using the ``order_parameter_query`` endpoint.


**Response**

On success (`HTTP` code ``201``, "created"), the request returns the following `JSON` data:

.. sourcecode:: javascript

    {
        "state": "TO_BE_CREATED",
        "btc_amount": <requested_amount_in_btc_as_float>,
        "btc_dest_address": <requested_destination_address_as_string>,
        "uuid": <unique_order_identifier_as_12_character_string>
    }

The field ``state`` reflects the state of an order. If ``state`` is ``TO_BE_CREATED`` in the
response, the order has been registered for creation and you can use the order ``uuid``
to start querying the order's status. All other fields should be self-explanatory.

On failure, one of the following errors is returned:

=========   ===================     ================================    ================
HTTP code   AEON.to error code       Error message/reason                Solution
=========   ===================     ================================    ================
``503``     ``AEONTO-ERROR-001``     internal services not available     try again later
``400``     ``AEONTO-ERROR-002``     malformed bitcoin address           check address validity
``400``     ``AEONTO-ERROR-003``     invalid bitcoin amount              check amount data type
``400``     ``AEONTO-ERROR-004``     bitcoin amount out of bounds        check min and max amount
``400``     ``AEONTO-ERROR-005``     unexpected validation error         contact support
``403``     ``AEONTO-ERROR-012``     too many requests                   try less often
=========   ===================     ================================    ================



Example
~~~~~~~

In this example, we create an order for donating 0.1 BTC to the Aeon project (using Bitcoin, ironically).

**Request**

.. sourcecode:: bash

    curl --data '{"btc_dest_address": "12Cyjf3qV6qLyXdzpLSLPdRFPUVidvnzFM", \
        "btc_amount": 0.1}' -H "Content-Type: application/json" https://aeon.to/api/v3/aeon2btc/order_create/

or

.. sourcecode:: bash

   http --json https://aeon.to/api/v3/aeon2btc/order_create/ btc_dest_address=12Cyjf3qV6qLyXdzpLSLPdRFPUVidvnzFM btc_amount=0.1

.. hint::
    Remember to set the `HTTP` Content-Type to ``application/json``!


**Response**

.. sourcecode:: javascript

    {
        "state": "TO_BE_CREATED",
        "btc_amount": 0.1,
        "btc_dest_address": "12Cyjf3qV6qLyXdzpLSLPdRFPUVidvnzFM",
        "uuid": "aeonto-5rpnYP"
    }


Rate limitation
~~~~~~~~~~~~~~~

Too many requests in a short time will result in a rate limitation of the endpoint.

It is possible to request the endpoint up to **4 times per minute**.


Creating a new order using a payment protocol URL
-------------------------------------------------

Reference
~~~~~~~~~

API endpoint: https://aeon.to/api/v3/aeon2btc/order_create_pp/

This alternative order creation endpoint allows to create a new order at the current price,
but instead of providing an explicit address and amount, the user provides a BIP70 url
that once fetched by AEON.to will provide the address and amount.

**Request**

Issue a `POST` request to create a new order supplying the following parameters:

.. sourcecode:: javascript

    {
        "pp_url": <payment_protocol_url>
    }

.. note::
    AEON.to is able to correct automatically URLs provided by users to the correct one serving a BIP70-protocol answer.
    For instance, values such as ``https://bitpay.com/invoice?id=xxx``, ``bitcoin:?r=https://bitpay.com/i/xxx`` will be
    corrected to the correct one automatically (the correct one being for `Bitpay`: ``https://bitpay.com/i/KbMdd4EhnLXSbpWGKsaeo6``.


**Response**

On success (`HTTP` code ``201``, "created"), the request returns the following `JSON` data:

.. sourcecode:: javascript

    {
        "state": "TO_BE_CREATED",
        "btc_amount": <requested_amount_in_btc_as_float>,
        "btc_dest_address": <requested_destination_address_as_string>,
        "uuid": <unique_order_identifier_as_12_character_string>,
        "pp_url": <payment_bip70_protocol_url>
    }

The field ``state`` reflects the state of an order. If ``state`` is ``TO_BE_CREATED`` in the
response, the order has been registered for creation and you can use the order ``uuid``
to start querying the order's status. All other fields should be self-explanatory.

On failure, one of the following errors is returned:

=========   ===================     ================================    ================
HTTP code   AEON.to error code       Error message/reason                Solution
=========   ===================     ================================    ================
``503``     ``AEONTO-ERROR-001``     internal services not available     try again later
``400``     ``AEONTO-ERROR-002``     malformed bitcoin address           check address validity
``400``     ``AEONTO-ERROR-003``     invalid bitcoin amount              check amount data type
``400``     ``AEONTO-ERROR-004``     bitcoin amount out of bounds        check min and max amount
``400``     ``AEONTO-ERROR-005``     unexpected validation error         contact support
``400``     ``AEONTO-ERROR-010``     payment protocol failed             invalid or outdated data served by url
``400``     ``AEONTO-ERROR-011``     malformed payment protocol url      url is malformed or cannot be contacted
``403``     ``AEONTO-ERROR-012``     too many requests                   try less often
=========   ===================     ================================    ================



Example
~~~~~~~

In this example, we create an order for donating 0.1 BTC to the Aeon developers (using Bitcoin, ironically).

**Request**

.. sourcecode:: bash

    curl --data '{"pp_url ": "https://bitpay.com/invoice?id=<invoice_id>"}' -H "Content-Type: application/json" https://aeon.to/api/v3/aeon2btc/order_create_pp/

or

.. sourcecode:: bash

   http --json https://aeon.to/api/v3/aeon2btc/order_create_pp/ pp_url="https://bitpay.com/invoice?id=<invoice_id>"

.. hint::
    Remember to set the `HTTP` Content-Type to ``application/json``!


**Response**

.. sourcecode:: javascript

    {
        "state": "TO_BE_CREATED",
        "btc_amount": 0.1,
        "btc_dest_address": "12Cyjf3qV6qLyXdzpLSLPdRFPUVidvnzFM",
        "uuid": "aeonto-XCZEsu",
        "pp_url": "https://bitpay.com/i/xxx"
    }


Rate limitation
~~~~~~~~~~~~~~~

Too many requests in a short time will result in a rate limitation of the endpoint.

It is possible to request the endpoint up to **4 times per minute**.

Querying order status
---------------------

Reference
~~~~~~~~~

API endpoint: https://aeon.to/api/v3/aeon2btc/order_status_query/

The order status endpoint allows users to query the status of an order, thereby obtaining payment details and order processing progress.

**Request**

Issue a `POST` request to query the status of a given order.
You have to supply the order's ``uuid`` in the request:

.. sourcecode:: javascript

    {
        "uuid": <unique_order_identifier_as_12_character_string>,
    }


**Response**

On success (`HTTP` code ``200``), the request returns the following `JSON` data:

.. sourcecode:: javascript

    {
        "state": <order_state_as_string>,
        "btc_amount": <requested_amount_in_btc_as_float>,
        "btc_dest_address": <requested_destination_address_as_string>,
        "uuid": <unique_order_identifier_as_12_character_string>
        "btc_num_confirmations": <btc_num_confirmations_as_integer>,
        "btc_num_confirmations_before_purge": <btc_num_confirmations_before_purge_as_integer>,
        "btc_transaction_id": <btc_transaction_id_as_string>,
        "created_at": <timestamp_as_string>,
        "expires_at": <timestamp_as_string>,
        "seconds_till_timeout": <seconds_till_timeout_as_integer>,
        "incoming_amount_total": <amount_in_aeon_for_this_order_as_float>,
        "remaining_amount_incoming": <amount_in_aeon_that_the_user_must_still_send_as_float>,
        "incoming_num_confirmations_remaining": <num_aeon_confirmations_remaining_before_bitcoins_will_be_sent_as_integer>,
        "incoming_price_btc": <price_of_1_btc_in_aeon_as_offered_by_service_as_float>,
        "receiving_address": <aeon_old_style_address_user_can_send_funds_to_as_string>,
        "receiving_integrated_address": <aeon_integrated_address_user_needs_to_send_funds_to_as_string>,
        "aeon_recommended_mixin": <aeon_recommended_mixin_as_integer>,
        "required_payment_id_long": <aeon_payment_id_user_needs_to_include_when_using_old_stlye_address_as_string>
        "required_payment_id_short": <aeon_payment_id_included_in_integrated_address_as_string>
    }

The user has to pay the order using the integrated address. In case the user's wallet does not support
integrated addresses, the user can pay via the old-style address while specifying the long payment id.

Presence of some of these fields depend on ``state``, which can take the following values:

====================    =============================================================
Value                   Meaning
====================    =============================================================
``TO_BE_CREATED``       order creation pending
``UNPAID``              waiting for Aeon payment by user
``UNDERPAID``           order partially paid
``PAID_UNCONFIRMED``    order paid, waiting for enough confirmations
``PAID``                order paid and sufficiently confirmed
``BTC_SENT``            bitcoin payment sent
``TIMED_OUT``           order timed out before payment was complete
``NOT_FOUND``           order wasn't found in system (never existed or was purged)
====================    =============================================================

All other fields should be self-explanatory.

On failure, one of the following errors is returned:

=========   ===================     ================================    ================
HTTP code   AEON.to error code       Error message/reason                Solution
=========   ===================     ================================    ================
``400``     ``AEONTO-ERROR-009``     invalid request                     check request parameters
``404``     ``AEONTO-ERROR-006``     requested order not found           check order UUID
``403``     ``AEONTO-ERROR-012``     too many requests                   try less often
=========   ===================     ================================    ================


Example
~~~~~~~

Continuing from our previous example, we can query the order by supplying the order's unique identifier ``uuid``.

**Request**

.. sourcecode:: bash

    curl --data '{"uuid": "aeonto-VkT2yM"}' -H "Content-Type: application/json" \
        https://aeon.to/api/v3/aeon2btc/order_status_query/

or

.. sourcecode:: bash

    http --json https://aeon.to/api/v3/aeon2btc/order_status_query/ uuid=aeonto-VkT2yM


**Response**

The response gives the current status of the order:

.. sourcecode:: javascript

    {
        "incoming_price_btc": 0.01760396,
        "uuid": "aeonto-XCZEsu",
        "state": "UNPAID",
        "btc_amount": 0.1,
        "btc_dest_address": "12Cyjf3qV6qLyXdzpLSLPdRFPUVidvnzFM",
        "receiving_address": "Sm6XWv4nmULGYQyHqRH54fgtXb3m3wnQ52EftauD2aMm1rmnrBwYDsudZj7MDgqB6Q1DgVR4jBgJdTFDZmssEdX711h3WyGzE",
        "receiving_integrated_address": "Sz4Dra9LBvxGYQyHqRH54fgtXb3m3wnQ52EftauD2aMm1rmnrBwYDsudZj7MDgqB6Q1DgVR4jBgJdTFDZmssEdX71ES97Z3M99r2Hv8KMQuE",
        "required_payment_id_long": "6db2414c87fd343fdd6ef4d73aa5f899372868da08faf4c0f5950a666ed08ad7",
        "required_payment_id_short": "7c855c2228e19671",
        "created_at": "2019-01-11T12:46:03Z",
        "expires_at": "2019-01-11T12:51:03Z",
        "seconds_till_timeout": 298,
        "incoming_amount_total": 14.4265105,
        "remaining_amount_incoming": 14.4265105,
        "incoming_num_confirmations_remaining": -1,
        "recommended_mixin": 4,
        "btc_num_confirmations_before_purge": 144,
        "btc_num_confirmations": 0,
        "btc_transaction_id": ""
    }

In this example, the next step would require the user to pay `14.4265105` AEON to the (integrated) Aeon
address `Sz4Dra...`.

In case the user's wallet does not support integrated addresses, the user can pay via the old-style
address `Sm6XWv...` while providing the (long) payment ID `6db241...`.

.. note::
    The payment **must** be made before the order expires, in this case, inside `298` seconds.


Rate limitation
~~~~~~~~~~~~~~~

Too many requests in a short time will result in a rate limitation of the endpoint.

It is possible to request the endpoint up to **3 times per second**.


Querying order price
---------------------

Reference
~~~~~~~~~

API endpoint: https://aeon.to/api/v3/aeon2btc/order_check_price/

The order status endpoint allows users to query the recent price of an order.

**Request**

Issue a `POST` request to query the price of a given order.
You have to supply the amount of BTC ``btc_amount`` in the request:

.. sourcecode:: javascript

    {
        "btc_amount": <requested_amount_in_btc_as_float>,
    }


**Response**

On success (`HTTP` code ``200``), the request returns the following `JSON` data:

.. sourcecode:: javascript

    {
        "btc_amount": <requested_amount_in_btc_as_float>,
        "incoming_amount_total": <amount_in_aeon_for_this_order_as_float>,
        "incoming_num_confirmations_remaining": <num_aeon_confirmations_remaining_before_bitcoins_will_be_sent_as_integer>,
        "incoming_price_btc": <price_of_1_btc_in_aeon_as_offered_by_service_as_float>
    }

On failure, one of the following errors is returned:

=========   ===================     ================================    ================
HTTP code   AEON.to error code       Error message/reason                Solution
=========   ===================     ================================    ================
``400``     ``AEONTO-ERROR-004``     bitcoin amount out of bounds        check min and max amount
``400``     ``AEONTO-ERROR-005``     unexpected validation error         contact support
``400``     ``AEONTO-ERROR-009``     invalid request                     check request parameters
``403``     ``AEONTO-ERROR-012``     too many requests                   try less often
=========   ===================     ================================    ================


Example
~~~~~~~

Imagine we want to check the recent price for an order including the payment of 0.15 BTC.

**Request**

.. sourcecode:: bash

    curl --data '{"btc_amount": "0.15"}' -H "Content-Type: application/json" \
        https://aeon.to/api/v3/aeon2btc/order_check_price/

or

.. sourcecode:: bash

    http --json https://aeon.to/api/v3/aeon2btc/order_check_price/ btc_amount=0.15


**Response**

The response gives the current price for the order:

.. sourcecode:: javascript

    {
        "btc_amount": 0.15,
        "incoming_amount_total": 2163.9765748,
        "incoming_num_confirmations_remaining": 1,
        "incoming_price_btc": 0.00006932
    }

In this example, the order including the payment of 0.15 BTC would require the user to pay `2163.9765748` AEON.


Rate limitation
~~~~~~~~~~~~~~~

Too many requests in a short time will result in a rate limitation of the endpoint.

It is possible to request the endpoint **once every 3 seconds**.
