Version 1
=========

Version 1 was the original version of the XMR.to API. Active from 2015 till July 2017.

.. note::
    API version 1 is deprecated since July 2017.

Querying order parameters
-------------------------

Reference
~~~~~~~~~

API endpoint: https://xmr.to/api/v1/xmr2btc/order_parameter_query/

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
        "price": <price_of_1_btc_in_xmr_as_offered_by_service_as_float>,
        "upper_limit": <upper_order_limit_in_btc_as_float>
    }

Fields should be self-explanatory.

On failure, one of the following errors is returned:

=========   ===================     =================================    ================
HTTP code   XMR.to error code       Error message/reason                 Solution
=========   ===================     =================================    ================
``503``     ``XMRTO-ERROR-001``     internal services not available      try again later
``503``     ``XMRTO-ERROR-007``     third party service not available    try again later
``503``     ``XMRTO-ERROR-008``     insufficient funds available         try again later
=========   ===================     =================================    ================


Example
~~~~~~~

**Request**

.. sourcecode:: bash

    curl https://xmr.to/api/v1/xmr2btc/order_parameter_query/

or

.. sourcecode:: bash

    http https://xmr.to/api/v1/xmr2btc/order_parameter_query/


**Response**

.. sourcecode:: javascript

    {
        "price": 0.004011,
        "upper_limit": 1.0,
        "lower_limit": 0.001
    }



Creating a new order
--------------------

Reference
~~~~~~~~~

API endpoint: https://xmr.to/api/v1/xmr2btc/order_create/

The order creation endpoint allows to create a new order at the current price.
The user has to supply a bitcoin destination address and amount to create the order.

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
HTTP code   XMR.to error code       Error message/reason                Solution
=========   ===================     ================================    ================
``503``     ``XMRTO-ERROR-001``     internal services not available     try again later
``400``     ``XMRTO-ERROR-002``     malformed bitcoin address           check address validity
``400``     ``XMRTO-ERROR-003``     invalid bitcoin amount              check amount data type
``400``     ``XMRTO-ERROR-004``     bitcoin amount out of bounds        check min and max amount
``400``     ``XMRTO-ERROR-005``     unexpected validation error         contact support
=========   ===================     ================================    ================



Example
~~~~~~~

In this example, we create an order for donating 0.1 BTC to the Monero developers (using Bitcoin, ironically).

**Request**

.. sourcecode:: bash

    curl --data '{"btc_dest_address": "1FhnVJi2V1k4MqXm2nHoEbY5LV7FPai7bb", \
        "btc_amount": 0.1}' -H "Content-Type: application/json" https://xmr.to/api/v1/xmr2btc/order_create/

or

.. sourcecode:: bash

   http --json https://xmr.to/api/v1/xmr2btc/order_create/ btc_dest_address=1FhnVJi2V1k4MqXm2nHoEbY5LV7FPai7bb btc_amount=0.1

.. hint::
    Remember to set the `HTTP` Content-Type to ``application/json``!


**Response**

.. sourcecode:: javascript

    {
        "state": "TO_BE_CREATED",
        "btc_amount": 0.1,
        "btc_dest_address": "1FhnVJi2V1k4MqXm2nHoEbY5LV7FPai7bb",
        "uuid": "xmrto-XCZEsu"
    }


Querying order status
---------------------

Reference
~~~~~~~~~

API endpoint: https://xmr.to/api/v1/xmr2btc/order_status_query/

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
        "xmr_amount_total": <amount_in_xmr_for_this_order_as_float>,
        "xmr_amount_remaining": <amount_in_xmr_that_the_user_must_still_send_as_float>,
        "xmr_num_confirmations_remaining": <num_xmr_confirmations_remaining_before_bitcoins_will_be_sent_as_integer>,
        "xmr_price_btc": <price_of_1_btc_in_xmr_as_offered_by_service_as_float>,
        "xmr_receiving_address": <xmr_address_user_needs_to_send_funds_to_as_string>,
        "xmr_required_amount": <xmr_amount_user_needs_to_send_as_float>,  (deprecated)
        "xmr_required_payment_id": <xmr_payment_id_user_needs_to_include_when_paying_as_string>
    }

.. note::
    The field `xmr_required_amount` is deprecated in favor of `xmr_amount_total`.

Presence of some of these fields depend on ``state``, which can take the following values:

====================    =============================================================
Value                   Meaning
====================    =============================================================
``TO_BE_CREATED``       order creation pending
``UNPAID``              waiting for Monero payment by user
``UNDERPAID``           order partially paid
``PAID_UNCONFIRMED``    order paid, waiting for confirmation
``PAID``                order paid and confirmed
``BTC_SENT``            bitcoin payment sent
``TIMED_OUT``           order timed out before payment was complete
``NOT_FOUND``           order wasn't found in system (never existed or was purged)
====================    =============================================================

All other fields should be self-explanatory.

On failure, one of the following errors is returned:

=========   ===================     ================================    ================
HTTP code   XMR.to error code       Error message/reason                Solution
=========   ===================     ================================    ================
``400``     ``XMRTO-ERROR-009``     invalid request                     check request parameters
``404``     ``XMRTO-ERROR-006``     requested order not found           check order UUID
=========   ===================     ================================    ================


Example
~~~~~~~

Continuing from our previous example, we can query the order by supplying the order's unique identifier ``uuid``.

**Request**

.. sourcecode:: bash

    curl --data '{"uuid": "xmrto-VkT2yM"}' -H "Content-Type: application/json" \
        https://xmr.to/api/v1/xmr2btc/order_status_query/

or

.. sourcecode:: bash

    http --json https://xmr.to/api/v1/xmr2btc/order_status_query/ uuid=xmrto-VkT2yM


**Response**

The response gives the current status of the order:

.. sourcecode:: javascript

    {
        "xmr_price_btc": 0.003963,
        "uuid": "xmrto-XCZEsu",
        "state_str": "UNPAID",
        "btc_amount": 0.1,
        "btc_dest_address": "1FhnVJi2V1k4MqXm2nHoEbY5LV7FPai7bb",
        "xmr_required_amount": 25.233409,
        "xmr_receiving_address": "44TVPcCSHebEQp4LnapPkhb2pondb2Ed7GJJLc6TkKwtSyumUnQ6QzkCCkojZycH2MRfLcujCM7QR1gdnRULRraV4UpB5n4",
        "xmr_required_payment_id":"223907873a29a00e3a5ff563c3b65f278ab6eb0cba623428ca3d9aaa54ea7bbb",
        "created_at": "2015-04-01T16:03:27Z",
        "expires_at": "2015-04-01T16:08:27Z",
        "seconds_till_timeout": 224,
        "xmr_amount_total": 25.233409,
        "xmr_amount_remaining": 25.233409,
        "xmr_num_confirmations_remaining": -1,
        "btc_num_confirmations_before_purge": 144,
        "btc_num_confirmations": 0,
        "btc_transaction_id": ""
    }

In this example, the next step would require the user to pay `25.233409` XMR to the Monero
address `44TV...B5n4`
while providing the payment ID `2239...7bbb`.

.. note::
    The payment **must** be made before the order expires, in this case, inside `224` seconds.

.. note::
    The field `xmr_required_amount` is deprecated in favor of `xmr_amount_total`.

