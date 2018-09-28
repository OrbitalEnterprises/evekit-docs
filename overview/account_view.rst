Account Screen
==============

An account screen is generated for each account you have registered with EveKit.  A typical screen
appears as follows:

   .. image:: account_overview.png
      :scale: 70%
      :alt: Account View
      :align: center

We'll explore each section in more detail below.

Summary
-------

The account summary displays basic information about the account and provides controls
for managing the ESI token used to access EVE Online data:

   .. image:: account_summary.png
      :scale: 120%
      :alt: Account Summary
      :align: center

Summary information is provided regardless of whether this account will record
character or corporation data.  The account name is listed at the top followed
by controls for modifying the account name (pencil icon), downloading the latest
account snapshot (cloud icon), and marking the account for removal (garbage can icon).
As noted elsewhere, accounts are not deleted until 24 hours after they are
marked for removal.  This gives you ample opportunity to change your mind.

Public information about the character and corporation associated with the account
is provided in the middle of the summary.

The bottom of the summary displays information about the ESI token used to
access data from EVE Online's servers.  If the token is still valid, then a green
"thumbs up" icon will be shown along with the date and time when the EveKit
backend will next need to refresh the token.  If the token is not valid, then
a red "thumbs down" icon will appear along with a button for re-authorizing
the token (not shown).  While the token is invalid, only public character and
corporation data will be recorded.  You'll need to re-authorize an invalid
token if you wish to continue recording non-public data.

On the right side of the token display, there are controls
which allow you to change token scopes, list the current scopes, or remove
the token completely.  If you remove the ESI token, then data will no longer be
recorded for this account.  However, you can continue to access any saved data
(using the model API) until you delete the account.

.. _ui-ModelAccessKeys:

Model Access Keys
-----------------

The model access keys section displays all EveKit API keys which can be used to
access the data recorded for this account:

   .. image:: account_keys.png
      :scale: 120%
      :alt: Account Access Keys
      :align: center

The plus icon on the top right is used to add new keys.

For each key, the key name is displayed followed by the key ID and hash, the expiry
date of the key, and the key limit.  The key ID and hash are needed by any application
which wishes to use the key to access account data.  The key expiry value gives the
date after which the key will no longer function.  The key limit value gives the
date of the oldest account data accessible with this key.  You can use key limits
to prevent key users from looking too far in the past with respect to account data.

To the right of each key listing are a set of controls for listing key permissions
(shield icon), using the key to explore account data (door icon), modifying
key settings - including key name, expiry and limit dates, and all permissions
(gear icon) - and finally, removing the key (garbage can icon).

If the 'explore account data' control is selected, then the view is changed
to the model API screen pre-populated with the credentials for this key.

Synchronization Status
----------------------

The last section in the account view shows the retrieval status (also called
synchronization status) of each endpoint for the given account:

   .. image:: account_sync.png
      :scale: 120%
      :alt: Account Sync Status
      :align: center

The reload icon on the top right can be used to refresh status.

The center of this section indicates the account type, and therefore the set of
endpoints which will be retrieved.  The date and time of the last retrieval
is recorded next to "Last Synch Time".  The next endpoint which will be attempted
is recorded next to "Next Synch Endpoint".

The left side of the display lists the endpoints specific to the account type.
The right side of the display lists endpoints shared between character and
corporation types.

Next to each endpoint is a status indicator showing the outcome of the most
recent retrieval attempt.  The status can be green (success), red (failure),
yellow (timeout), or grey (not attempted - see below).  Hovering over the status
shows the time of the last attempt, and any detail regarding the status.

A grey status indicates one of the following conditions:

* The required scope is not included in the ESI token; or,

* Retrieval of the endpoint has been disabled by the administrator.  This usually
  occurs either because of an EveKit bug (check the blog for status), or an
  ESI bug.


