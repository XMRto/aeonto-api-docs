
Public test instance
====================

AEON.to offers a public test instance to ease development on services depening on
its API. The instance is connected to the Aeon stage network and Bitcoin test network.
(The Aeon test network is intended for protocol development and is not suitable
to test service integration).

You can create an order, send Aeon stagenet coins to AEON.to, and will receive
Bitcoin testnet coins on successful order execution.

.. hint::
   The addresses used look different from mainnet ones: Aeon stagenet addresses
   start with `S`, while Bitcoin testnet addresses start with `m`, `n` or `2`.

Access
------

The public test instance is accessible under https://test.aeon.to

Funding
-------

As there is no BTC/AEON exchange available on the stagenet, the hotwallet of
the public test instance cannot be automatically refilled. If the service
runs low (i.e., the max order is very small), simply send Bitcoin testnet
coins to the following address:

::

    mxRHStxLBEFfL5WavorMjB6ADrD8rCA2hC

