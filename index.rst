.. EveKit documentation master file, created by
   sphinx-quickstart on Fri Aug 24 21:22:03 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to EveKit!
==================

`EveKit <https://evekit.orbital.enterprises>`_ is a web-based toolkit for building applications
based on the `EVE Online <https://www.eveonline.com>`_ third party APIs.  The primary function
of EveKit is to periodically retrieve and store your character and corporation data using the
`EVE Swagger Interface <https://esi.evetech.net>`_.  EveKit attempts to retrieve your data 
as frequently as the ESI timers allow.  Updates to your data are stored in a time series,
allowing for both current and historical views.  You can access your data directly via
our REST API; or, you can use a third party tool which supports EveKit.

.. _overview:

.. toctree::
   :maxdepth: 4
   :caption: User Guide

   overview/features
   overview/getting_started
   overview/ui_guide
   overview/account_view
   overview/api_view
   overview/user_settings

.. _data-model:

.. toctree::
   :maxdepth: 4
   :caption: Data Model

   model/model
   model/sync
	     
.. _integrations:

.. toctree::
   :maxdepth: 4
   :caption: Integrations

   integrations/jeveassets
.. integrations/googlesheets
.. integrations/jupyter

.. _other-data:

.. toctree::
   :maxdepth: 4
   :caption: Dataset Details

   other/sde
   other/ref
   other/marketdata

