.. XMR.to API documentation master file, created by
   sphinx-quickstart on Thu Apr  2 12:14:27 2015.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to the XMR.to API documentation!
========================================

.. image:: _static/xmrto-twitter-logo.png
    :target: https://xmr.to
    :alt: XMR.to logo

This is the API documentation for XMR.to_, which
is a service that allows users to pay any bitcoin address
anonymously using Monero_. 

Follow us on Twitter if you want to get updates about XMR.to: https://twitter.com/xmr_to

.. note::
    The current version of the API is 2.

Contents
--------

.. toctree::
    :maxdepth: 3

    introduction
    api_v2
    api_v1
    test_instance
    problems

Preface
-------

:Author: XMR.to support
:Contact: support@xmr.to
:Organization: XMR.to
:Copyright: Copyright (C) 2015-2019 XMR.to
            
            Permission is hereby granted, free of charge, to any person obtaining a
            copy of this software and associated documentation files (the "Software"),
            to deal in the Software without restriction, including without limitation
            the rights to use, copy, modify, merge, publish, distribute, sublicense,
            and/or sell copies of the Software, and to permit persons to whom the
            Software is furnished to do so, subject to the following conditions:

            The above copyright notice and this permission notice shall be included in
            all copies or substantial portions of the Software.

            THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS
            OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
            FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
            AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
            LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
            FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
            DEALINGS IN THE SOFTWARE.

:Abstract:  XMR.to_ is a service that allows users to pay any bitcoin address
            anonymously using Monero.
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

.. meta::
   :keywords: xmr.to, monero, bitcoin, cryptocurrency
   :description lang=en: XMR.to is a service that allows users to pay any bitcoin address
                         anonymously using Monero.

.. _XMR.to: https://xmr.to
.. _Monero: https://getmonero.org


