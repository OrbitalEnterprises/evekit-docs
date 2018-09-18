Features
========

EveKit was designed to provide the following key features:

* **Web Ready**: EveKit is designed for easy access via common web protocols.  Every API is annotated with `Swagger <https://swagger.io/>`_
  markup, making it very easy to work directly with our REST services (resturning JSON).  You can also generate a
  client in the language of your choice using free tools like the `Swagger Editor <https://swagger.io/tools/swagger-editor/>`_.

* **History**: What differentiates EveKit from other EVE Online tools is the retention of a complete history of all your account data.
  Each aspect of your account data is organized into a time series.  This means that we version your data so that each time EveKit
  performs an update (attempted as frequently as the EVE API cache timers allow), we store any changes in a new version.  An API
  argument let's you decide whether you want to look at the latest version of your data, or some version recorded in the past.
  
* **Meta-Data**: Account data we store on your behalf can be tagged with persistent meta data.  'Persistent' means this data is preserved across
  all versions of your account data.  Meta data makes it possible to store custom data you might need for certain applications.  In particular,
  meta data simplifies the creation of EveKit extensions (documented elsewhere).

* **Bulk Snapshots**: The latest version of all of your account data is always available to you in bulk form (as an archive of CSV files).
  We recognize that not everyone will want to use our APIs to access their data, so we generate daily snapshots which you can access
  from the account summary.

* **Related APIs**: EveKit also provides APIs for important public data such as ESI reference data (for example, alliance membership),
  the Static Data Export (SDE), and market data.  Some of these APIs are exposed via REST, while others are exposed as bulk file downloads.

