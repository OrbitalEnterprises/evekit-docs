.. _market-data-details:

Market Data
===========

As described in :ref:`api-markets`, the EveKit Market Data API consists of two services:

1. An *online* service for retrieving book and market history snapshots of individual types and regions; and,

2. An *archive* which stores order books and market history in bulk format.

In this section, we describe the services provided by the archive.

Overview
--------

EveKit archives three forms of market data:

1. *Market History* records price and volume data for each type in each region on each day.
   This data is retrieved from the ``/markets/{region_id}/history/`` ESI endpoint.

2. *Order Books* record snapshots of the market order book for each type in each region
   at five minute intervals during each day.  This data is retrieved from the
   ``/markets/{region_id}/orders/`` ESI endpoint.  Five minute snapshots are recorded
   because this is the minimum resolution (e.g. cache timer) allowed by the ESI endpoint
   for this data.

3. *Inferred Trades* are the estimated set of trades (e.g. volume, price and
   direction - buy or sell) which occurred for a given type in a given region on
   a given day.  The ESI does not provide this data.  Instead, EveKIt attempts to infer
   the set of trades based on changes to the order book during the day.

All market data is archived to Google storage and is available at the following prefix:

.. code:: bash

	  https://storage.googleapis.com/evekit_md/YYYY/MM/DD

where *YYYY*, *MM* and *DD*, are the requested year, month and day, respectively.

The following table describes the set of files stored for each day:

+--------------------------------+----------------------+-----------------------------------------------+
|**File Name**                   |**Typical Size**      |**Description**                                |
+--------------------------------+----------------------+-----------------------------------------------+
|*market_YYYYMMDD.tgz*           |Less than 5 MB.       |All market history for all types and regions   |
|                                |                      |for the given day.                             |
+--------------------------------+----------------------+-----------------------------------------------+
|*interval_YYYYMMDD_5.tgz*       |About 200 MB.         |All order book data for all types and regions  |
|                                |                      |for the given day.  The value, *5*, in the name|
|                                |                      |of the file indicates that these are 5 minute  |
|                                |                      |snapshots.                                     |
+--------------------------------+----------------------+-----------------------------------------------+
|*trades_allregions_YYYYMMDD.gz* |Less than 5 MB.       |All inferred trades for all types and regions  |
|                                |                      |for the given day.                             |
+--------------------------------+----------------------+-----------------------------------------------+
|*market_YYYYMMDD.bulk*          |Less than 5 MB.       |Market history for all types and regions in    |
|                                |                      |*bulk* format, designed to be partially        |
|                                |                      |retrieved via an HTTP "range" request.         |
+--------------------------------+----------------------+-----------------------------------------------+
|*interval_YYYYMMDD_5.bulk*      |About 500 MB.         |Order book data for all types and regions in   |
|                                |                      |*bulk* format, designed to eb partially        |
|                                |                      |retrieved via an HTTP "range" request.  The    |
|                                |                      |value, *5*, in the name of the file indicates  |
|                                |                      |that these are 5 minute snapshots.             |
+--------------------------------+----------------------+-----------------------------------------------+
|*market_YYYYMMDD.index.gz*      |Less than 100 KB.     |Index file which provides offsets into the     |
|                                |                      |*bulk* market history file based on EVE market |
|                                |                      |type ID.                                       |
+--------------------------------+----------------------+-----------------------------------------------+
|*interval_YYYYMMDD_5.index.gz*  |Less than 200 KB.     |Index file which provides offsets into the     |
|                                |                      |*bulk* order book file based on EVE market type|
|                                |                      |ID.  The value, *5*, in the name of the file   |
|                                |                      |indicates that these are 5 mnute snapshots.    |
+--------------------------------+----------------------+-----------------------------------------------+

Files (and segments of files) are gzip compressed to save space.  Files are generated on
a schedule as follows (*t* is the date for which market data is recorded):

+-----------------+----------------+--------------------------------------------+
|**File Type**    |**Availability**|**Notes**                                   |
|                 |                |                                            |
+-----------------+----------------+--------------------------------------------+
|Market History   |*t+2* 0800 UTC  |Market history is not available via ESI     |
|                 |                |until approximately *t+1* 1200 UTC at which |
|                 |                |point it can be collected and processed by  |
|                 |                |EveKit.                                     |
+-----------------+----------------+--------------------------------------------+
|Order Book       |*t+1* 0800 UTC  |Assembly starts at approximately *t+1* 0200 |
|                 |                |UTC                                         |
+-----------------+----------------+--------------------------------------------+
|Inferred Trades  |*t+1* 2000 UTC  |Trades can not be inferred until order books|
|                 |                |have been processed.                        |
+-----------------+----------------+--------------------------------------------+

We describe the format of each file type below.
   
Market History
--------------

Market history data records the order count, high price, low price, average price and volume
for a given type in a given region.  The market history archive consists of collection of files
in CSV format, one file per market type ID, with one line per region as follows:

+--------------------------------+-----------------------------------------------+
|**Field**                       |**Description**                                |
+--------------------------------+-----------------------------------------------+
|*TYPE*                          |EVE market type ID.                            |
+--------------------------------+-----------------------------------------------+
|*REGION*                        |EVE market region ID.                          |
+--------------------------------+-----------------------------------------------+
|*ORDER COUNT*                   |Number of market orders executed for this type |
|                                |in this region on this day.                    |
+--------------------------------+-----------------------------------------------+
|*LOW PRICE*                     |Low trade price for this type in this region on|
|                                |this day.                                      |
+--------------------------------+-----------------------------------------------+
|*HIGH PRICE*                    |High trade price for this type in this region  |
|                                |on this day.                                   |
+--------------------------------+-----------------------------------------------+
|*AVERAGE PRICE*                 |Average trade price for this type in this      |
|                                |region on this day.                            |
+--------------------------------+-----------------------------------------------+
|*VOLUME*                        |Daily volume for this type in this region.     |
+--------------------------------+-----------------------------------------------+
|*DATE*                          |Market history date in milliseconds UTC (since |
|                                |the epoch).                                    |
+--------------------------------+-----------------------------------------------+

Files of the form *market_YYYYMMDD.tgz* are a compressed archive containing all
history files for all types for a given day.  You should retrieve this file if
you wish to operate on a large number of market types for a given day.  A sample
listing for a recent file looks as follows:

.. code:: bash
  
	  $ tar tzf market_20180929.tgz | head -n 10
	  market_18_20180929.history.gz
	  market_19_20180929.history.gz
	  market_20_20180929.history.gz
	  market_21_20180929.history.gz
	  market_22_20180929.history.gz
	  market_34_20180929.history.gz
	  market_35_20180929.history.gz
	  market_36_20180929.history.gz
	  market_37_20180929.history.gz
	  market_38_20180929.history.gz

At time of writing, there are about 8600 files (e.g. EVE market types) in a given archive.
Each file in the archive is a compressed CSV file in the format shown in the table above.
The first file in this example appears as follows:

.. code:: bash
	  
	  $ gzcat market_18_20180929.history.gz | head -n 10
	  18,10000028,2,56.01,56.01,56.01,9269,1538179200000
	  18,10000030,40,30.00,51.01,51.01,979837,1538179200000
	  18,10000016,92,59.36,61.02,60.80,2769268,1538179200000
	  18,10000064,79,52.30,52.30,52.30,4939877,1538179200000
	  18,10000002,135,58.29,58.31,58.29,6972853,1538179200000
	  18,10000068,94,53.50,53.50,53.50,16821756,1538179200000
	  18,10000069,1,58.00,58.00,58.00,111,1538179200000
	  18,10000048,4,52.01,52.01,52.01,434038,1538179200000
	  18,10000042,62,55.01,55.01,55.01,7323977,1538179200000
	  18,10000043,3,56.81,56.81,56.81,133,1538179200000

In most cases, market history files are small enough to simply download and extract.
However, some applications (e.g. web sites) may have insufficient local storage
to make this possible.  For these cases, the *bulk* files can be used to extract
just the data of interest.  Extracting data from a bulk file involves two steps:

1. Read an index file and find the offset of the desired market type; then,

2. Use a "range" HTTP request to read the appropriate data from the bulk file.

Index files are formatted as a compressed list of pairs giving each file name and
an offset into the corresponding bulk file where data for the given file is stored.
Using the example from above, we can pull the first few lines from the index file
as follows:

.. code:: bash

	  $ curl -s https://storage.googleapis.com/evekit_md/2018/09/29/market_20180929.index.gz | gzcat | head -n 10
	  market_18_20180929.history.gz 0
	  market_19_20180929.history.gz 808
	  market_20_20180929.history.gz 1302
	  market_21_20180929.history.gz 2631
	  market_22_20180929.history.gz 3108
	  market_34_20180929.history.gz 3489
	  market_35_20180929.history.gz 6430
	  market_36_20180929.history.gz 9295
	  market_37_20180929.history.gz 12195
	  market_38_20180929.history.gz 14936

Suppose, for example, we would like to read market history for market type 34 (Tritanium).  To read this data, we
need to read data starting from offset 3489 and ending at offset 6429 (one less than the offset for market type 35).
We can perform this extraction using a range request via curl.  Note that the data is gzip compressed in
the bulk file, so we need to decompress to read the results:

.. code:: bash

	  $ curl -s -H "range: bytes=3489-6429" https://storage.googleapis.com/evekit_md/2018/09/29/market_20180929.bulk | gzcat | head -n 10
	  34,10000025,3,5.30,5.30,5.30,497677,1538179200000
	  34,10000028,25,3.80,3.80,3.80,140731594,1538179200000
	  34,10000029,102,6.00,6.00,6.00,196253830,1538179200000
	  34,10000030,259,5.21,5.37,5.31,316489920,1538179200000
	  34,10000031,8,4.99,4.99,4.99,49742860,1538179200000
	  34,10000016,438,3.58,5.00,4.35,53128283,1538179200000
	  34,10000020,187,4.50,4.87,4.67,66906196,1538179200000
	  34,10000022,2,0.10,0.10,0.10,1303633,1538179200000
	  34,10000023,3,6.00,6.00,6.00,30002,1538179200000
	  34,10000009,2,6.00,6.00,6.00,1029740,1538179200000

Note that you can read the entire bulk data file by removing the "range" argument.  Given the
small size of the market data files, this may be a more appropriate approach if you require
history for several different types.
	  
CAUTION: Reading Bulk Files with Zlib/Node.js
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Bulk files are created by compressing and then concatenating the individual files stored in the
compressed archive (e.g. the ".tgz" file).  If you read a bulk file from start to finish you
are therefore reading the concatenation of several individually compressed files.  The standard
GZip libraries are more than happy to handle a stream of concatenated gzip files.
At time of writing, however, the standard ZLib library in Node.js does not handle a stream
properly and will always stop reading at the end of the first compressed file.  To work around
this limitation, you'll either need to use a different compression library, or partition
the data stream and read each partition individually.  One way to partition the stream is to
look for the GZip magic header bytes.  This is the approach taken in the
`EveKit Market Data Server <https://github.com/OrbitalEnterprises/evekit-marketdata-server>`_
when requesting historic data.  The code for splitting the buffer can be found
`here <https://github.com/OrbitalEnterprises/evekit-marketdata-server/blob/master/api/helpers/evekit_market.js>`_.

Order Books
-----------

Order book data records the set of buy and sell orders for a given type in a given region at
a given time.  The ESI refreshes order book data every five minutes.  For a given day, there are
therefore 288 snapshots for each type in each region.  As with market history, an order
book archive consists of a collection of files, one file per market type ID.  Each file
consists of 288 market snapshots for all regions where order book data was available for
a given type.  A sample listing for a recent file looks as follows:

.. code:: bash

	  $ tar tzf interval_20180929_5.tgz | head -n 10
	  interval_18_20180929_5.book.gz
	  interval_19_20180929_5.book.gz
	  interval_20_20180929_5.book.gz
	  interval_21_20180929_5.book.gz
	  interval_22_20180929_5.book.gz
	  interval_34_20180929_5.book.gz
	  interval_35_20180929_5.book.gz
	  interval_36_20180929_5.book.gz
	  interval_37_20180929_5.book.gz
	  interval_38_20180929_5.book.gz

The format for order book files is slightly more complicated:

.. code:: bash

	  TYPE
	  SNAPSHOTS_PER_REGION
	  FIRST_REGION_ID
	  FIRST_REGION_FIRST_SNAPSHOT_TIME
	  FIRST_REGION_FIRST_SNAPSHOT_BUY_ORDER_COUNT
	  FIRST_REGION_FIRST_SNAPSHOT_SELL_ORDER_COUNT
	  FIRST_REGION_FIRST_SNAPSHOT_BUY_ORDER
	  ...
	  FIRST_REGION_FIRST_SNAPSHOT_SELL_ORDER
	  ...
	  FIRST_REGION_SECOND_SNAPSHOT_TIME
	  ...
	  SECOND_REGION_ID
	  ...

The first example in the listing above appears as follows:

.. code:: bash

	  $ gzcat interval_18_20180929_5.book.gz | head -n 10
	  18
	  288
	  10000025
	  1538179200000
	  0
	  0
	  1538179500000
	  0
	  0
	  1538179800000

In this example, the market type is Plagioclase (18) and each region will have 288 snapshots.
The first region is Immensea (10000025) and the first snapshot is at 20180929 0000 UTC.
There were no buy or sell orders at this time for this type in this region.  The remaining
snapshots will be sampled at five minute intervals.  The next snapshot is therefore at
20180929 0005 UTC and likewise has no buy or sell orders.

When buy or sell orders occur, they are recorded in CSV format with the following fields:

+--------------------------------+-------------------------------------------------+
|**Field**                       |**Description**                                  |
+--------------------------------+-------------------------------------------------+
|*ORDER ID*                      |Unique market order ID.                          |
+--------------------------------+-------------------------------------------------+
|*BUY*                           |"true" for buy orders, "false" otherwise.        |
+--------------------------------+-------------------------------------------------+
|*ISSUED*                        |Order issue date in milliseconds TUC (since the  |
|                                |epoch).                                          |
+--------------------------------+-------------------------------------------------+
|*PRICE*                         |Order price.                                     |
+--------------------------------+-------------------------------------------------+
|*VOLUME ENTERED*                |Volume entered when order was created.           |
+--------------------------------+-------------------------------------------------+
|*MINIMUM VOLUME*                |Minimum volume requied for each order fill.      |
+--------------------------------+-------------------------------------------------+
|*VOLUME*                        |Current remaining volume to be filled in the     |
|                                |order.                                           |
+--------------------------------+-------------------------------------------------+
|*ORDER RANGE*                   |Order range string.  One of "station",           |
|                                |"solarsystem", "region" or a number representing |
|                                |the number of jumps allowed from the station     |
|                                |where the order was placed.                      |
+--------------------------------+-------------------------------------------------+
|*LOCATION ID*                   |Location ID of station where order was entered.  |
+--------------------------------+-------------------------------------------------+
|*DURATION*                      |Order duration in days.                          |
+--------------------------------+-------------------------------------------------+

As an example, consider Tritanium (market type 34) from the example above:

.. code:: bash

	  $ gzcat interval_34_20180929_5.book.gz | head -n 10
	  34
	  288
	  10000025
	  1538179200000
	  3
	  0
	  5245221025,true,1536237612000,5.48,40000000,1,18803539,1,1027496149370,90
	  5252251352,true,1538069032000,4.15,25000000,1,3313490,1,1027011062756,90
	  5255545769,true,1537577368000,4.02,10000000,1,10000000,5,1027011062756,90
	  1538179500000

In this example, there were three buy orders in the first snapshot for Immensea.
For convenience, **buy orders are always sorted highest price first**.
Likewise, **sell orders are always sorted lowest price first**.

With 288 snapshots across approximately 8600 market types and 100 regions, order book
files are significantly larger than market history.  In most cases, it will be more
appropriate to extract needed data from an online bulk file instead of extracting
the entire archive.

Data can be extracted from order book bulk files using the same technique as for
market history bulk files.  First, the index file is consulted to discover the
appropriate offsets for the requested data.  Here are the first few lines from our
example date:

.. code:: bash

	  curl -s https://storage.googleapis.com/evekit_md/2018/09/29/interval_20180929_5.index.gz | gzcat | head -n 10
	  interval_18_20180929_5.book.gz 0
	  interval_19_20180929_5.book.gz 93941
	  interval_20_20180929_5.book.gz 146174
	  interval_21_20180929_5.book.gz 289411
	  interval_22_20180929_5.book.gz 343002
	  interval_34_20180929_5.book.gz 404514
	  interval_35_20180929_5.book.gz 737280
	  interval_36_20180929_5.book.gz 985574
	  interval_37_20180929_5.book.gz 1252779
	  interval_38_20180929_5.book.gz 1495309

A range request will then extract the requested data.  As with market history, each range is gzip compressed and must
be decompressed in order to read the results.  The following example retrieves Tritanium from the online
bulk file:

.. code:: bash

	  curl -s -H "range: bytes=404514-737279" https://storage.googleapis.com/evekit_md/2018/09/29/interval_20180929_5.bulk | gzcat | head -n 10 
	  34
	  288
	  10000025
	  1538179200000
	  3
	  0
	  5245221025,true,1536237612000,5.48,40000000,1,18803539,1,1027496149370,90
	  5252251352,true,1538069032000,4.15,25000000,1,3313490,1,1027011062756,90
	  5255545769,true,1537577368000,4.02,10000000,1,10000000,5,1027011062756,90
	  1538179500000

Inferred Trades
---------------

Inferred trade data is an estimation of trades that occurred in a given region on
a given data.  The ESI does not provide this data.  Instead, we estimate likely
trades by inspecting changes in the order book.  The process by which trades
are estimated is documented `here <https://github.com/OrbitalEnterprises/evekit-source-mdtools/blob/master/src/extract_trades.py>`_.

All trades are collected into a single file per day.  The format is similar to
the order book format:

.. code:: bash

	  TYPE_ID
	  NUMBER_OF_REGIONS
	  REGION_1
	  TRADE_COUNT
	  TRADE_1
	  ...
	  REGION_2
	  TRADE_COUNT
	  ...
	  TYPE_ID
	  NUMBER_OF_REGIONS
	  ...

The list of trades per region is in CSV format with the following fields:

+--------------------------------+-----------------------------------------------+
|**Field**                       |**Description**                                |
+--------------------------------+-----------------------------------------------+
|*TIMESTAMP*                     |Trade timestamp in milliseconds UTC (since the |
|                                |epoch).                                        |
+--------------------------------+-----------------------------------------------+
|*ACTUAL*                        |"True" if this represents an actual trade,     |
|                                |"False" if this trade is inferred.             |
+--------------------------------+-----------------------------------------------+
|*BUY*                           |"True" if this trade was a buy from an existing|
|                                |sell order, otherwise this trade was a sell to |
|                                |an existing buy order.                         |
+--------------------------------+-----------------------------------------------+
|*MATCHED ORDER ID*              |The order ID from which the buy or sell        |
|                                |occurred.                                      |
+--------------------------------+-----------------------------------------------+
|*TRADE PRICE*                   |Trade price.                                   |
|                                |                                               |
+--------------------------------+-----------------------------------------------+
|*TRADE VOLUME*                  |Trade volume.                                  |
|                                |                                               |
+--------------------------------+-----------------------------------------------+
|*LOCATION*                      |The location ID of the station where the trade |
|                                |occurred, or "None" if it was not possible to  |
|                                |determine location.                            |
+--------------------------------+-----------------------------------------------+

The following example shows the contents of the trade file for our example date:

.. code:: bash

	  $ gzcat trades_allregions_20180929.gz | head -n 10
	  18
	  14
	  10000002
	  82
	  1538180400000,True,True,5259222306,58.42,28570,None
	  1538180700000,True,True,5259222306,58.42,46,None
	  1538180700000,True,True,5257674674,50.03,85,None
	  1538182200000,True,True,5259222306,58.42,22,None
	  1538183100000,True,True,5259222306,58.42,8847,None
	  1538188200000,True,True,5259222306,58.42,102,None

In this example, the first set of trades are reported for Plagioclase (market type 18).
Trades were inferred for 14 regions on this date.  The first region reported is
"The Forge" which has 82 total trades.  The first trade is an actual buy reported
at 0020 UTC.  The trade was for 28570 units at a price of 58.42 ISK.  A location
could not be inferred.  This indicates that the matched sell order had range
greater than "station".
