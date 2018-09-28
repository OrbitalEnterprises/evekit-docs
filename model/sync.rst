.. _model_sync:

Model Synchronization and Access
================================

EveKit data is updated through a process called model synchronization.  During
model synchronization, data is retrieved from ESI endpoints and compared to
the current live version of various data models.  If new data is encountered,
or previous data has been removed, then EveKit's model objects are updated as
described in the previous section.  EveKit attempts to look for new data at
least as frequently as the ESI cache timers allow, and as often as load on the
EveKit servers allow.  At time of writing, the shortest interval between EveKit
updates is 7 minutes.  The fastest ESI cache timer is about five seconds
(e.g. character location).

Endpoint to Model Data Mapping
------------------------------

The following table shows the mapping from ESI endpoint(s) to EveKit model object(s).

+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|**ESI endpoint**                                                  |**EveKit Models Updated**                   |**Notes**                                 |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /alliances/{alliance_id}/contacts/                              |* :ref:`model-Contact`                      |Contacts and contact labels are updated   |
|                                                                  |                                            |simultaneously during synchronization.    |
|* /alliances/{alliance_id}/contacts/labels/                       |* :ref:`model-ContactLabel`                 |                                          |
|                                                                  |                                            |                                          |
|* /characters/{character_id}/contacts/labels/                     |                                            |                                          |
|                                                                  |                                            |                                          |
|* /characters/{character_id}/contacts/                            |                                            |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/contacts/labels/                 |                                            |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/contacts/                        |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/assets/                              |* :ref:`model-Asset`                        |Assets, their locations and their names   |
|                                                                  |                                            |are updated simultaneously during         |
|* /characters/{character_id}/assets/locations/                    |* :ref:`model-Location`                     |synchronization.  Note that               |
|                                                                  |                                            |:ref:`model-Location` stores both location|
|* /characters/{character_id}/assets/names/                        |                                            |and name.                                 |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/assets/                          |                                            |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/assets/locations/                |                                            |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/assets/names/                    |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/blueprints/                          |* :ref:`model-Blueprint`                    |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/blueprints/                      |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/bookmarks/                           |* :ref:`model-Bookmark`                     |EveKit does not store a separate bookmark |
|                                                                  |                                            |folder object.  Instead, bookmark and     |
|* /characters/{character_id}/bookmarks/folders/                   |                                            |folder information are combined in the    |
|                                                                  |                                            |:ref:`model-Bookmark` object.  A          |
|* /corporations/{corporation_id}/bookmarks/                       |                                            |consequence is that empty folder names are|
|                                                                  |                                            |not stored.  This may change in the       |
|* /corporations/{corporation_id}/bookmarks/folders/               |                                            |future.                                   |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/calendar/                            |* :ref:`model-UpcomingCalendarEvent`        |Calendar events and their attendees are   |
|                                                                  |                                            |updated simultaneously during             |
|* /characters/{character_id}/calendar/{event_id}/                 |* :ref:`model-CalendarEventAttendee`        |synchronization.                          |
|                                                                  |                                            |                                          |
|* /characters/{character_id}/calendar/{event_id}/attendees/       |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/contracts/                           |* :ref:`model-Contract`                     |Contracts, contract items and contract    |
|                                                                  |                                            |bids (for auctions) are updated           |
|* /characters/{character_id}/contracts/{contract_id}/bids/        |* :ref:`model-ContractItem`                 |simultaneously during synchronization.    |
|                                                                  |                                            |                                          |
|* /characters/{character_id}/contracts/{contract_id}/items/       |* :ref:`model-ContractBid`                  |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/contracts/                       |                                            |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/contracts/{contract_id}/bids/    |                                            |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/contracts/{contract_id}/items/   |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/fw/stats/                            |* :ref:`model-FacWarStats`                  |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/fw/stats/                        |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/fittings/                            |* :ref:`model-Fitting`                      |                                          |
|                                                                  |                                            |                                          |
|                                                                  |* :ref:`model-FittingItem`                  |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/fleet/                               |* :ref:`model-CharacterFleet`               |Character fleet information including     |
|                                                                  |                                            |fleet composition is updated              |
|* /fleets/{fleet_id}/                                             |* :ref:`model-FleetInfo`                    |simultaneously during synchronization.    |
|                                                                  |                                            |                                          |
|* /fleets/{fleet_id}/members/                                     |* :ref:`model-FleetMember`                  |                                          |
|                                                                  |                                            |                                          |
|* /fleets/{fleet_id}/wings/                                       |* :ref:`model-FleetWing`                    |                                          |
|                                                                  |                                            |                                          |
|                                                                  |* :ref:`model-FleetSquad`                   |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/industry/jobs/                       |* :ref:`model-IndustryJob`                  |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/industry/jobs/                   |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/killmails/recent/                    |* :ref:`model-Kill`                         |Kill mail processing is sharded by        |
|                                                                  |                                            |killmail ID due to size.  As a result,    |
|* /corporations/{corporation_id}/killmails/recent/                |* :ref:`model-KillItem`                     |several synchronization rounds are        |
|                                                                  |                                            |necessary to process all recent killmail. |
|* /killmails/{killmail_id}/{killmail_hash}/                       |* :ref:`model-KillVictim`                   |At time of writing, 10 rounds are         |
|                                                                  |                                            |necessary to completely process all recent|
|                                                                  |* :ref:`model-KillAttacker`                 |kill mail.  During a round, the sharded   |
|                                                                  |                                            |kill list and detailed kill information   |
|                                                                  |                                            |are updated simultaneously.               |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/location/                            |* :ref:`model-CharacterLocation`            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/loyalty/points/                      |* :ref:`model-LoyaltyPoints`                |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/mail/                                |* :ref:`model-CharacterMailMessage`         |Mail processing is sharded by mail ID due |
|                                                                  |                                            |to size.  As a result, several            |
|* /characters/{character_id}/mail/{mail_id}/                      |* :ref:`model-MailingList`                  |synchronization rounds are necessary to   |
|                                                                  |                                            |process all mail.  At time of writing, 20 |
|* /characters/{character_id}/mail/lists/                          |* :ref:`model-MailLabel`                    |rounds are necessary to completely process|
|                                                                  |                                            |all mail.  During a round, the sharded    |
|* /characters/{character_id}/mail/labels/                         |                                            |mail list and detailed mail information   |
|                                                                  |                                            |are updated simultaneously.  Mailing list |
|                                                                  |                                            |and mail label processing is not sharded  |
|                                                                  |                                            |and will be updated in a single           |
|                                                                  |                                            |synchronization round.                    |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/orders/                              |* :ref:`model-MarketOrder`                  |Market orders and market order history are|
|                                                                  |                                            |processed simultaneously during           |
|* /characters/{character_id}/orders/history/                      |                                            |synchronization.                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/orders/                          |                                            |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/orders/history/                  |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/medals/                              |* :ref:`model-CharacterMedal`               |                                          |
|                                                                  |                                            |                                          |
|                                                                  |* :ref:`model-CharacterMedalGraphic`        |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/mining/                              |* :ref:`model-MiningLedger`                 |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/notifications/                       |* :ref:`model-CharacterContactNotification` |Notifications and contact notifications   |
|                                                                  |                                            |are processed simultaneously during       |
|* /characters/{character_id}/notifications/contacts/              |* :ref:`model-CharacterNotification`        |synchronization.                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/online/                              |* :ref:`model-CharacterOnline`              |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/opportunities/                       |* :ref:`model-Opportunity`                  |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/planets/                             |* :ref:`model-PlanetaryColony`              |The planet list and detailed planet       |
|                                                                  |                                            |information are processed simultaneously  |
|* /characters/{character_id}/planets/{planet_id}/                 |* :ref:`model-PlanetaryRoute`               |during synchronization.                   |
|                                                                  |                                            |                                          |
|                                                                  |* :ref:`model-PlanetaryLink`                |                                          |
|                                                                  |                                            |                                          |
|                                                                  |* :ref:`model-PlanetaryPin`                 |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/agents_research/                     |* :ref:`model-ResearchAgent`                |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/roles/                               |* :ref:`model-CharacterRole`                |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/clones/                              |* :ref:`model-CharacterSheetClone`          |                                          |
|                                                                  |                                            |                                          |
|                                                                  |* :ref:`model-JumpClone`                    |                                          |
|                                                                  |                                            |                                          |
|                                                                  |* :ref:`model-JumpCloneImplant`             |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/implants/                            |* :ref:`model-Implant`                      |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/fatigue/                             |* :ref:`model-CharacterSheetJump`           |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/                                     |* :ref:`model-CharacterSheet`               |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/ship/                                |* :ref:`model-CharacterShip`                |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/skillqueue/                          |* :ref:`model-SkillInQueue`                 |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/skills/                              |* :ref:`model-CharacterSheetAttributes`     |A character's skill list and attributes   |
|                                                                  |                                            |are processed simultaneously during       |
|* /characters/{character_id}/attributes/                          |* :ref:`model-CharacterSheetSkillPoints`    |synchronization.                          |
|                                                                  |                                            |                                          |
|                                                                  |* :ref:`model-CharacterSkill`               |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/standings/                           |* :ref:`model-Standing`                     |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/standings/                       |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/titles/                          |* :ref:`model-CharacterTitle`               |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/wallet/                              |* :ref:`model-AccountBalance`               |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/wallets/                         |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/wallet/journal/                      |* :ref:`model-WalletJournal`                |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/wallets/{division}/journal/      |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /characters/{character_id}/wallet/transactions/                 |* :ref:`model-WalletTransaction`            |                                          |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/wallets/{division}/transactions/ |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/containers/logs/                 |* :ref:`model-ContainerLog`                 |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/customs_offices/                 |* :ref:`model-CustomsOffice`                |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/divisions/                       |* :ref:`model-Division`                     |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/facilities/                      |* :ref:`model-Facility`                     |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/medals/                          |* :ref:`model-CorporationMedal`             |Corporation medals and issued medals are  |
|                                                                  |                                            |processed simultaneously during           |
|* /corporations/{corporation_id}/medals/issued/                   |* :ref:`model-CorporationMemberMedal`       |synchronization.                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/members/                         |* :ref:`model-MemberRole`                   |Corporation member list, roles and history|
|                                                                  |                                            |of role changes are processed             |
|* /corporations/{corporation_id}/roles/                           |* :ref:`model-MemberRoleHistory`            |simultaneously during synchronization.    |
|                                                                  |                                            |                                          |
|* /corporations/{corporation_id}/roles/history/                   |* :ref:`model-Member`                       |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/members/limit/                   |* :ref:`model-MemberLimit`                  |Corporation member limit and member       |
|                                                                  |                                            |tracking are processed simultaneously     |
|* /corporations/{corporation_id}/membertracking/                  |* :ref:`model-MemberTracking`               |during synchronization.                   |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporation/{corporation_id}/mining/extractions/               |* :ref:`model-MiningExtraction`             |Mining extractions, observer list, and    |
|                                                                  |                                            |observations are processed simultaneously |
|* /corporation/{corporation_id}/mining/observers/                 |* :ref:`model-MiningObserver`               |during synchronization.                   |
|                                                                  |                                            |                                          |
|* /corporation/{corporation_id}/mining/observers/{observer_id}/   |* :ref:`model-MiningObservation`            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/shareholders/                    |* :ref:`model-Shareholder`                  |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/                                 |* :ref:`model-CorporationSheet`             |Corporation sheet and icons are processed |
|                                                                  |                                            |simultaneously during synchronization.    |
|* /corporations/{corporation_id}/icons/                           |                                            |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/starbases/                       |* :ref:`model-Starbase`                     |Starbase list and starbase details are    |
|                                                                  |                                            |processed simultaneously during           |
|* /corporations/{corporation_id}/starbases/{starbase_id}/         |* :ref:`model-Fuel`                         |synchronization.                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/structures/                      |* :ref:`model-Structure`                    |                                          |
|                                                                  |                                            |                                          |
|                                                                  |* :ref:`model-StructureService`             |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
|* /corporations/{corporation_id}/titles/                          |* :ref:`model-CorporationTitle`             |Corporation titles, roles and titles      |
|                                                                  |                                            |assigned to members are processed         |
|* /corporations/{corporation_id}/members/titles/                  |* :ref:`model-CorporationTitleRole`         |simultaneously during synchronization.    |
|                                                                  |                                            |                                          |
|                                                                  |* :ref:`model-MemberTitle`                  |                                          |
+------------------------------------------------------------------+--------------------------------------------+------------------------------------------+
 
Access Permission to Model Data Mapping
---------------------------------------

The following table gives the mapping from EveKit Access Key Permissions to
the model data objects which can be retrieved with those permissions.

+-------------------------------+-------------------------------+-------------------------------------------+
|**Permission**                 |**Mask**                       |**EveKit Models**                          |
+-------------------------------+-------------------------------+-------------------------------------------+
|Account Status Access          |ACCESS_ACCOUNT_STATUS          |* :ref:`model-CharacterOnline`             |
+-------------------------------+-------------------------------+-------------------------------------------+
|Account Balance Access         |ACCESS_ACCOUNT_BALANCE         |* :ref:`model-AccountBalance`              |
+-------------------------------+-------------------------------+-------------------------------------------+
|Asset Access                   |ACCESS_ASSETS                  |* :ref:`model-Asset`                       |
+-------------------------------+-------------------------------+-------------------------------------------+
|Character Sheet Access         |ACCESS_CHARACTER_SHEET         |* :ref:`model-CharacterRole`               |
|                               |                               |                                           |
|                               |                               |* :ref:`model-CharacterSheet`              |
|                               |                               |                                           |
|                               |                               |* :ref:`model-CharacterSheetAttributes`    |
|                               |                               |                                           |
|                               |                               |* :ref:`model-CharacterSheetClone`         |
|                               |                               |                                           |
|                               |                               |* :ref:`model-CharacterSheetJump`          |
|                               |                               |                                           |
|                               |                               |* :ref:`model-CharacterSheetSkillPoints`   |
|                               |                               |                                           |
|                               |                               |* :ref:`model-CharacterSkill`              |
|                               |                               |                                           |
|                               |                               |* :ref:`model-CharacterTitle`              |
|                               |                               |                                           |
|                               |                               |* :ref:`model-Implant`                     |
|                               |                               |                                           |
|                               |                               |* :ref:`model-JumpClone`                   |
|                               |                               |                                           |
|                               |                               |* :ref:`model-JumpCloneImplant`            |
|                               |                               |                                           |
|                               |                               |* :ref:`model-LoyaltyPoints`               |
|                               |                               |                                           |
|                               |                               |* :ref:`model-Opportunity`                 |
+-------------------------------+-------------------------------+-------------------------------------------+
|Corporation Sheet              |ACCESS_CORPORATION_SHEET       |* :ref:`model-CorporationSheet`            |
|                               |                               |                                           |
|                               |                               |* :ref:`model-Division`                    |
+-------------------------------+-------------------------------+-------------------------------------------+
|Contact List Access            |ACCESS_CONTACT_LIST            |* :ref:`model-Contact`                     |
|                               |                               |                                           |
|                               |                               |* :ref:`model-ContactLabel`                |
+-------------------------------+-------------------------------+-------------------------------------------+
|Blueprint Access               |ACCESS_BLUEPRINTS              |* :ref:`model-Blueprint`                   |
+-------------------------------+-------------------------------+-------------------------------------------+
|Bookmark Access                |ACCESS_BOOKMARKS               |* :ref:`model-Bookmark`                    |
+-------------------------------+-------------------------------+-------------------------------------------+
|Contract Access                |ACCESS_CONTRACTS               |* :ref:`model-Contract`                    |
|                               |                               |                                           |
|                               |                               |* :ref:`model-ContractBid`                 |
|                               |                               |                                           |
|                               |                               |* :ref:`model-ContractItem`                |
+-------------------------------+-------------------------------+-------------------------------------------+
|Faction War Stats Access       |ACCESS_FAC_WAR_STATS           |* :ref:`model-FacWarStats`                 |
+-------------------------------+-------------------------------+-------------------------------------------+
|Industry Jobs Access           |ACCESS_INDUSTRY_JOBS           |* :ref:`model-IndustryJob`                 |
|                               |                               |                                           |
|                               |                               |* :ref:`model-Facility`                    |
+-------------------------------+-------------------------------+-------------------------------------------+
|Kill Log Access                |ACCESS_KILL_LOG                |* :ref:`model-Kill`                        |
|                               |                               |                                           |
|                               |                               |* :ref:`model-KillAttacker`                |
|                               |                               |                                           |
|                               |                               |* :ref:`model-KillItem`                    |
|                               |                               |                                           |
|                               |                               |* :ref:`model-KillVictim`                  |
+-------------------------------+-------------------------------+-------------------------------------------+
|Locations Access               |ACCESS_LOCATIONS               |* :ref:`model-CharacterLocation`           |
|                               |                               |                                           |
|                               |                               |* :ref:`model-CharacterShip`               |
|                               |                               |                                           |
|                               |                               |* :ref:`model-Location`                    |
+-------------------------------+-------------------------------+-------------------------------------------+
|Market Orders Access           |ACCESS_MARKET_ORDERS           |* :ref:`model-MarketOrder`                 |
+-------------------------------+-------------------------------+-------------------------------------------+
|Mining Ledger Access           |ACCESS_MINING_LEDGER           |* :ref:`model-MiningLedger`                |
|                               |                               |                                           |
|                               |                               |* :ref:`model-MiningExtraction`            |
|                               |                               |                                           |
|                               |                               |* :ref:`model-MiningObservation`           |
|                               |                               |                                           |
|                               |                               |* :ref:`model-MiningObserver`              |
+-------------------------------+-------------------------------+-------------------------------------------+
|Standings Access               |ACCESS_STANDINGS               |* :ref:`model-Standing`                    |
+-------------------------------+-------------------------------+-------------------------------------------+
|Wallet Journal Access          |ACCESS_WALLET_JOURNAL          |* :ref:`model-WalletJournal`               |
+-------------------------------+-------------------------------+-------------------------------------------+
|Wallet Transactions Access     |ACCESS_WALLET_TRANSACTIONS     |* :ref:`model-WalletTransaction`           |
+-------------------------------+-------------------------------+-------------------------------------------+
|Meta-Data Modification         |ALLOW_METADATA_CHANGES         |N/A - This mask allows write access to the |
|                               |                               |meta-data map for any model object for     |
|                               |                               |which the access key has read access.      |
|                               |                               |                                           |
+-------------------------------+-------------------------------+-------------------------------------------+
|Calendar Event Attendee Access |ACCESS_CALENDAR_EVENT_ATTENDEES|* :ref:`model-CalendarEventAttendee`       |
+-------------------------------+-------------------------------+-------------------------------------------+
|Access Chat Channels           |ACCESS_CHAT_CHANNELS           |* :ref:`model-ChatChannel`                 |
|                               |                               |                                           |
|                               |                               |* :ref:`model-ChatChannelMember`           |
+-------------------------------+-------------------------------+-------------------------------------------+
|Access Character Fleets        |ACCESS_CHARACTER_FLEETS        |* :ref:`model-CharacterFleet`              |
|                               |                               |                                           |
|                               |                               |* :ref:`model-FleetInfo`                   |
|                               |                               |                                           |
|                               |                               |* :ref:`model-FleetMember`                 |
|                               |                               |                                           |
|                               |                               |* :ref:`model-FleetSquad`                  |
|                               |                               |                                           |
|                               |                               |* :ref:`model-FleetWing`                   |
+-------------------------------+-------------------------------+-------------------------------------------+
|Access Contact Notifications   |ACCESS_CONTACT_NOTIFICATIONS   |* :ref:`model-CharacterContactNotification`|
+-------------------------------+-------------------------------+-------------------------------------------+
|Access Ship Fittings           |ACCESS_FITTINGS                |* :ref:`model-Fitting`                     |
|                               |                               |                                           |
|                               |                               |* :ref:`model-FittingItem`                 |
+-------------------------------+-------------------------------+-------------------------------------------+
|Access Mail                    |ACCESS_MAIL                    |* :ref:`model-CharacterMailMessage`        |
|                               |                               |                                           |
|                               |                               |* :ref:`model-MailLabel`                   |
+-------------------------------+-------------------------------+-------------------------------------------+
|Access Mailing Lists           |ACCESS_MAILING_LISTS           |* :ref:`model-MailingList`                 |
+-------------------------------+-------------------------------+-------------------------------------------+
|Access Medals                  |ACCESS_MEDALS                  |* :ref:`model-CharacterMedal`              |
|                               |                               |                                           |
|                               |                               |* :ref:`model-CharacterMedalGraphic`       |
+-------------------------------+-------------------------------+-------------------------------------------+
|Access Notifications           |ACCESS_NOTIFICATIONS           |* :ref:`model-CharacterNotification`       |
+-------------------------------+-------------------------------+-------------------------------------------+
|Access Research                |ACCESS_RESEARCH                |* :ref:`model-ResearchAgent`               |
+-------------------------------+-------------------------------+-------------------------------------------+
|Access Skill Queue             |ACCESS_SKILL_QUEUE             |* :ref:`model-SkillInQueue`                |
+-------------------------------+-------------------------------+-------------------------------------------+
|Upcoming Calendar Event Access |ACCESS_UPCOMING_CALENDAR_EVENTS|* :ref:`model-UpcomingCalendarEvent`       |
+-------------------------------+-------------------------------+-------------------------------------------+
|Container Log Access           |ACCESS_CONTAINER_LOG           |* :ref:`model-ContainerLog`                |
+-------------------------------+-------------------------------+-------------------------------------------+
|Corporation Medals Access      |ACCESS_CORPORATION_MEDALS      |* :ref:`model-CorporationMedal`            |
+-------------------------------+-------------------------------+-------------------------------------------+
|Member Medals Access           |ACCESS_MEMBER_MEDALS           |* :ref:`model-CorporationMemberMedal`      |
+-------------------------------+-------------------------------+-------------------------------------------+
|Member Security Access         |ACCESS_MEMBER_SECURITY         |* :ref:`model-Member`                      |
|                               |                               |                                           |
|                               |                               |* :ref:`model-MemberRole`                  |
|                               |                               |                                           |
|                               |                               |* :ref:`model-MemberTitle`                 |
+-------------------------------+-------------------------------+-------------------------------------------+
|Member Security Log Access     |ACCESS_MEMBER_SECURITY_LOG     |* :ref:`model-MemberRoleHistory`           |
+-------------------------------+-------------------------------+-------------------------------------------+
|Member Tracking Access         |ACCESS_MEMBER_TRACKING         |* :ref:`model-MemberLimit`                 |
|                               |                               |                                           |
|                               |                               |* :ref:`model-MemberTracking`              |
+-------------------------------+-------------------------------+-------------------------------------------+
|Shareholder Access             |ACCESS_SHAREHOLDERS            |* :ref:`model-Shareholder`                 |
+-------------------------------+-------------------------------+-------------------------------------------+
|Starbase List Access           |ACCESS_STARBASE_LIST           |* :ref:`model-Fuel`                        |
|                               |                               |                                           |
|                               |                               |* :ref:`model-Starbase`                    |
+-------------------------------+-------------------------------+-------------------------------------------+
|Structure List Access          |ACCESS_STRUCTURES              |* :ref:`model-Structure`                   |
|                               |                               |                                           |
|                               |                               |* :ref:`model-StructureService`            |
+-------------------------------+-------------------------------+-------------------------------------------+
|Corporation Titles Access      |ACCESS_CORPORATION_TITLES      |* :ref:`model-CorporationTitle`            |
|                               |                               |                                           |
|                               |                               |* :ref:`model-CorporationTitleRole`        |
+-------------------------------+-------------------------------+-------------------------------------------+
