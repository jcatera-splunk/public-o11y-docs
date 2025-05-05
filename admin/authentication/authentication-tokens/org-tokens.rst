.. _admin-org-tokens:

********************************************************************************
Create and manage organization access tokens using Splunk Observability Cloud
********************************************************************************

.. meta::
   :description: Create and manage organization access tokens: defaults, manage, visibility, change a token, rename, or disable.

Access tokens, also known as org tokens, are long-lived organization-level tokens. You can use access tokens in all API requests except those that require a token associated with a user who has administrative access. See :ref:`admin-api-access-tokens` for more information.

Use access tokens to:

- Send data points to Splunk Observability Cloud with API calls.
- Run scripts that call the API.
- Manage your resource by tracking usage for different groups of users, services, teams, and so on. For example, you have users in the U.S. and Canada sending data to Splunk Observability Cloud. You can give each group its specific access token to compare the amount of data coming from each country.

.. note:: By default, only users who are administrators can search for and view all access tokens. You can change this default when you create or update an access token.

Power users who have access to tokens in an organization see a banner, but only admins will get an email saying that the tokens must be rotated.

Token expiry 
================

You can view the expiration dates of your tokens through the access token page. To view this page, select :guilabel:`Settings` and select :guilabel:`Access tokens`. By default, access tokens expire 30 days after the creation date. You can rotate a token before it expires, or you can change the default expiration date during token creation. For details, see :ref:`access-token-rotate` and :ref:`create-access-token-date`.

By default, every organization admin receives an email 30 days before a token in their org expires. The email includes a link to Splunk Observability Cloud that displays a list of expiring tokens. To change the expiration reminder date, see :ref:`create-access-token-date`.

The default access token
===========================

By default, every organization has one organization-level access token. If you don't create any additional tokens, every API request that sends data to Splunk Observability Cloud must use this access token.

.. _manage-access-token:

Manage access tokens
=======================

To manage your access (org) tokens, follow these steps:

#. Open the :guilabel:`Settings` menu.
#. Select :guilabel:`Access Tokens`.
#. Find your token by using the :guilabel:`Status` and :guilabel:`Scope` filters or enter the token name in the search bar.
#. Select the expand icon next to the token name. This displays details about the token.

   For information about the access token permissions allowed by the :guilabel:`Authorization Scopes` field value, see the permissions step in :ref:`create-access-token`.
#. (Optional) If you're an organization administrator, the actions menu (|verticaldots|) appears to the right side of the token listing. You can select token actions from this menu.

#. See :ref:`change-token-permissions` and :ref:`change-token-expiration` to modify token permissions and token expiration settings, respectively.

.. _change-token-permissions:

Change token permissions
-------------------------------------

If you're an organization administrator, you can change token permissions for other users and teams.

To change the token permissions, follow these steps:

#. Select the :guilabel:`Access Token Permissions` box. Choose from the following permission options:

      * :menuselection:`Only Admins can Read`: Only admin users can view or read the new token. The token isn't visible to other users.
      * :menuselection:`Admins and Select Users or Teams can Read`: Admin users and users or teams you select can view or read the new token. The token isn't visible to anyone else.
      * :menuselection:`Everyone can Read`: Every user and team in the organization can view and read the token.

#. To add permissions, select the left arrow below :guilabel:`Access Token Permissions`.
#. If you selected :guilabel:`Admins and Select Users or Teams can Read`, select the users or teams to whom you want to give access.
#. To remove a team or user, select the delete icon (:strong:`X`) next to the team or username.
#. To update the token, select :guilabel:`Update`.

.. _change-token-expiration:

Change token expiration date and expiration alerts
-------------------------------------------------------

To change the token expiration date and expiration alerts, follow these steps:

#. In the token actions menu (|verticaldots|), select :guilabel:`Expiration date`.
#. In the :guilabel:`Expiration date` box, select a new expiration date for the token.
#. To change the visibility of the expiration alert, select from the following options:

   * :menuselection:`Admins and users or teams with token permissions can receive alert`: Admins and anyone with token permissions receive an alert when the token is close to expiring.
   * :menuselection:`Only admins can receive alert`: Only admins receive an alert when the token is close to expiring.

#. Configure the type of alert that your recipients receive.
#. Change the time at which recipients receive an alert. For example, a value of ``7d`` means recipients receive an alert 7 days before the token expires.
#. Select :guilabel:`Update`.

View and copy access token secrets
====================================

To view the token secret, select the token name and then select :guilabel:`Show Token`.

To copy the token value, select :guilabel:`Copy`. You don't need to be an administrator to view or copy an access token.


.. _create-access-token:

Create an access token
==========================

To get started with creating an access token, follow these steps: 

#. Open the Splunk Observability Cloud main menu.
#. Select :menuselection:`Settings` and select :menuselection:`Access Tokens`.
#. Select :guilabel:`New Token`.

Next, complete each step in the access token creation guided setup:

* :ref:`create-access-token-name`.
* :ref:`create-access-token-permissions`.
* :ref:`create-access-token-date`.

.. note::

   You must be an organization administrator to create access tokens.

.. _create-access-token-name:

Name the token and select the authorization scope
-------------------------------------------------------------------------

To get started with creating the token, enter a name and scope for the token. Complete the following steps:

#. Enter a unique token name. If you enter a token name that is already in use, even if the token is inactive, Splunk Observability Cloud doesn't accept the name.
#. Select an authorization scope. See the following table for information about the authorization scopes:

   .. list-table::
      :header-rows: 1

      * - Authorization scope
        - Description
      * - RUM token
        - Use this scope to authenticate with RUM ingest endpoints. These endpoints use the following base URL: ``https://rum-ingest.<REALM>.signalfx.com/v1/rum``.
      * - Ingest token
        - Use this scope to authenticate with data ingestion endpoints and when using the Splunk Distribution of OpenTelemetry Collector. These endpoints use the following base URLs:

          * POST :code:`https://ingest.<REALM>.signalfx.com/v2/datapoint`
          * POST :code:`https://ingest.<REALM>.signalfx.com/v2/datapoint/otlp`
          * POST :code:`https://ingest.<REALM>.signalfx.com/v2/event`
          * POST :code:`https://ingest.<REALM>.signalfx.com/v1/trace`

           For information about these endpoints, see :new-page:`Sending data points <https://dev.splunk.com/observability/docs/datamodel/ingest/>`.
      * - API token
        - Use this scope to authenticate with Splunk Observability Cloud API endpoints. These endpoints use the following base URLs:

          * :code:`https://api.<REALM>.signalfx.com`
          * :code:`wss://stream.<REALM>.signalfx.com`

           When you create an access token with API authentication scope, select at least one Splunk Observability Cloud role to associate with the token. You can select from ``power``, ``usage``, or ``read_only``. To learn more about Splunk Observability Cloud roles, see :ref:`roles-and-capabilities`.

           For information about these endpoints, see :new-page:`Summary of Splunk Observability Cloud API Endpoints <https://dev.splunk.com/observability/docs/apibasics/api_list/>`.

#. (Optional) Add a description for the token.
#. Select :guilabel:`Next` to continue to the next step.

.. _create-access-token-permissions:

Determine who can view and use the token
--------------------------------------------------------

Next, configure token permissions so your organization's users and teams can use the token. Complete the following steps:

#. Edit the visibility permissions. To display the available permissions, select the :guilabel:`Access Token Permissions` box. The following
   permission options appear:

      * :menuselection:`Only Admins can Read`: Only admin users can view or read the new token. The token isn't visible to other users.
      * :menuselection:`Admins and Select Users or Teams can Read`: Admin users and users or teams you select can view or read the new token. The token isn't visible to anyone else.
      * :menuselection:`Everyone can Read`: Every user and team in the organization can view and read the token.
   
   To add permissions, select the arrow below :guilabel:`Access Token Permissions`.

#. If you selected :guilabel:`Admins and Select Users or Teams can Read`, select the users or teams to whom you want to give access:

   #. Select :guilabel:`Add Team or User`. Splunk Observability Cloud displays a list of teams and users in your organization.
   #. To find the team or username in a large list, start entering the name in the search box. Splunk Observability Cloud returns matching results.
      Select the user or team.
   #. To add more teams or users, select :guilabel:`Add Team or User` again.

      .. note::

         You might see the following message in the middle of the dialog:

         You are currently giving permissions to a team with Restrict Access deactivated. This means any user can join this team and can access this Access Token.

         This message means that all users are able to join the team and then view or read the access token.

   #. To remove a team or user, select the delete icon (:strong:`X`) next to the team or username.

#. Select :guilabel:`Next` to continue to the final step.

.. _create-access-token-date:

Configure an expiration date
-----------------------------------------------

To finish creating the token, select an expiration date for the token. 

#. In the :guilabel:`Expiration date` box, select a date at which the token will expire. The date can't be over 18 years from the token creation date.
#. In the :guilabel:`Expiration alert` box, select from one of the following options:

   * :menuselection:`Only admins can receive alert`: Only admins receive an alert when the token is close to its expiration date.
   * :menuselection:`Admins and users or teams with token permissions can receive alert`: Admins and any users with token permissions receive an alert when the token is close to its expiration date.
#. Select :guilabel:`Create` to finish creating the new token.

.. _access-token-rotate:

Rotate an access token
==============================

You can rotate an access token using the access token menu or the Splunk Observability Cloud API. This creates a new secret for the token and deactivates the token's previous secret. Optionally, you can provide a grace period before the previous token secret expires.

You can't rotate tokens after they expire. If you don't rotate a token before it expires, you must create a new token to replace it.

.. note:: You must be a Splunk Observability Cloud admin to rotate a token. 

Rotate access tokens using the token menu
-------------------------------------------------------------------

To rotate a token using the access token menu, follow these steps:

#. In Splunk Observability Cloud, select :guilabel:`Settings`.
#. Select :guilabel:`Access tokens`. 
#. In the access tokens menu, select the token you want to rotate.
#. Select :guilabel:`Rotate token`.
#. Enter an expiration date for the new token secret, and optionally, a grace period for the current token secret. 
#. Select :guilabel:`Rotate`.

After you're finished rotating the token, update any of your OpenTelemetry Collector configurations with the new token secret before the grace period ends. 

Rotate access tokens using the Splunk Observability Cloud API
-------------------------------------------------------------------

To rotate an access token with the API, use the ``POST /token/{name}/rotate`` endpoint in the Splunk Observability Cloud API. An API call to rotate a token looks like this:

.. code-block:: bash

   curl -X  POST "https://api.{realm}.signalfx.com/v2/token/{name}/rotate?graceful={gracePeriod}&secondsUntilExpiry={secondsUntilExpiry}" \
      -H "Content-type: application/json" \
      -H "X-SF-TOKEN: <your-user-session-api-token-value>"

Follow these steps:

#. Enter your Splunk realm in the ``realm`` field.
#. Enter your API session token in the ``your-user-session-api-token-value`` field. To find or create an API session token, see :ref:`admin-api-access-tokens`.
#. Provide the name of the token you want to rotate in the ``name`` field.
#. Optionally, provide a grace period, in seconds, in the ``gracePeriod`` field.
#. Optionally, provide the seconds until your token expires in the ``secondsUntilExpiry`` field. This can be any value between 0 second and 5,676,000,000 seconds (18 years), inclusive. If left unspecified, the token remains valid for 30 days. 
#. Call the API endpoint to rotate the token.

For example, the following API call rotates ``myToken`` and sets a grace period of 604800 seconds (7 days) before the previous token secret expires.

.. code-block:: bash

   curl -X POST "https://api.us0.signalfx.com/v2/token/myToken/rotate?graceful=6048000" \
      -H "Content-type: application/json" \
      -H "X-SF-TOKEN: <123456abcd>"

After you're finished rotating the token, update any of your OpenTelemetry Collector configurations with the new token secret before the grace period ends. 

To learn more about this endpoint and to see more examples of requests and responses, see the :new-page:`Splunk developer documentation <https://dev.splunk.com/observability/reference/api/org_tokens/latest#endpoint-rotate-token-secret>`. 

Rename an access token
=========================

To rename a token:

#. Select :menuselection:`Edit Token` from the token's actions menu (|verticaldots|).
#. Enter a new name for the token.
#. Select :guilabel:`OK`.

Renaming a token does not affect the token's secret.

.. note::

   For :ref:`Cloud integrations (AWS, GCP, or Azure) <get-started-connect>`, after renaming an access token you need to select a new token name using the API. For AWS, you can also set up a new token :ref:`in the UI <aws-wizardconfig>`.

Deactivate or activate an access token
========================================

.. note::

   You can't delete tokens. You can only deactivate them.

To deactivate a token, select :menuselection:`Deactivate` from the token's actions menu (|verticaldots|).

To activate a deactivated token, select :menuselection:`Activate` from the deactivated token's actions menu (|verticaldots|). 

You can search for activated or deactivated tokens using the :guilabel:`Status` filter in the access tokens page.

Manage token limits
=========================================

To change limits for your access tokens, including host and container limits, follow these steps:

#. Select the token that you want to edit. This opens the token detail page.
#. Select the token actions menu (|verticaldots|), and select :guilabel:`Manage limits`. 
#. In the :guilabel:`Manage limits` menu, add the new token limits. 

To learn more about token limits, see :ref:`admin-manage-usage`.
