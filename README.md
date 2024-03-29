# Vorco API Scripts

Sample scripts for Vorco customers to use for automation purposes.

If there's a task available through our customer portal you're interested in automating which a script is not available here for, or if you need assistance with retrieving your API key or service IDs, [contact us](https://www.vorco.net/contact-us).

To use the sample scripts, edit the variables in the top of the script as needed, then run from PowerShell. They can be set up to run as scheduled tasks if required e.g. using Task Scheduler in Windows.

# API Authentication

To be able to query the Vorco API, obtain an access_token by making a POST to the login endpoint at ```https://extranet.vorco.net/api/v1/login``` including a header named "Authorization" with a value of "Token $apiKey" where $apiKey is the API key already provided to you by Vorco Support.

Store the access_token provided in response so you can use it to manage a SkyPhone service or retrieve Calling for Teams CDR's and recordings.

## cURL Token Request Example

```
curl --location --request POST 'https://extranet.vorco.net/api/v1/login' \
--header 'Authorization: Token $APIKey'
```

# Teams CDR Records and Call Recordings

This allows you to automatically retrieve Calling for Teams Call Detail Records and Call Recordings.

## CDR Retrieval

Using your [access_token](#api-authentication), make queries for CDR's by making a GET request to the CDR endpoint at ```https://extranet.vorco.net/api/v1/ms_call_services/$teams_service_id/cdrs```. $teams_service_id is provided to you by Vorco Support.
Include a header named "Authorization" with a value of "Bearer $access_token" where $access_token is the access_token you obtained already, plus URL parameters of:
- start_date in YYYY-MM-DD format
- end_date in YYYY-MM-DD format

CDR's will be returned including an URL attribute that is a link to download the recording. You can loop over the returned CDR's to retrieve each recording, but must still include the Authorization Bearer token as a header.

### cURL CDR Retrieval Example

```
curl --location --request GET 'https://extranet.vorco.net/api/v1/ms_call_services/$teams_service_id/cdrs?start_date=2022-01-01&end_date=2022-01-31' \
--header 'Authorization: Bearer $access_token'
```

## Sample Script - Retrieve Teams CDRs and Recordings.ps1
This script allows you to automate retrieval of CDRs and Recordings for a given number of days, and will automatically zip them for archival purposes.

# SkyPhone Call Forward Destinations
This allows you to automate updating call forwarding settings.

## Get current call forward settings

Using your [access_token](#api-authentication), make a GET request to the call forward endpoint at ```https://extranet.vorco.net/api/v1/centrex_users/$CentrexUserId/features/call_forwards```. $CentrexUserId is provided to you by Vorco Support.
Include a header named "Authorization" with a value of "Bearer $access_token" where $access_token is the access_token you obtained already.

Current call forward settings will be returned.

### cURL Call Forwarding Settings Retrieval Example

```
curl --location --request GET 'https://extranet.vorco.net/api/v1/centrex_users/$CentrexUserId/features/call_forwards' \
--header 'Authorization: Bearer $access_token'
```

## Update call forward settings

Using your [access_token](#api-authentication), make a PUT request to the call forward endpoint at ```https://extranet.vorco.net/api/v1/centrex_users/$CentrexUserId/features/call_forwards```. $CentrexUserId is provided to you by Vorco Support.
Include a header named "Authorization" with a value of "Bearer $access_token" where $access_token is the access_token you obtained already, plus a header named "Content-Type" with a value of "Content-Type: application/x-www-form-urlencoded", plus in the body a x-www-form-urlencoded string of ```call_forward_always%5Bnumber%5D=$NewForward```. $NewForward is the new phone number to forward to.

### cURL Call Forwarding Settings Update Example

```
curl --location --request PUT 'https://extranet.vorco.net/api/v1/centrex_users/$CentrexUserId/features/call_forwards' \
--header 'Authorization: Bearer $access_token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data 'call_forward_always%5Bnumber%5D=$NewForward'
```

## Sample Script - Update SkyPhone Call Forward Destination.ps1
This script allows you to set a list of phone numbers to cycle through for the call forward destination of a SkyPhone line.
One use case for this is on a scheduled task to run once a week to cycle through who is on call after hours for the week.
