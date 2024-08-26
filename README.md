# AWS and Salesforce Integrations
## Quick Start Guide
### Prerequisite

To enable Salesforce integrations using AWS services, follow these steps:

Create [Salesforce connected app](https://help.salesforce.com/s/articleView?id=sf.connected_app_client_credentials_setup.htm&type=5) and enable [Enable OAuth Settings](https://help.salesforce.com/s/articleView?id=sf.connected_app_create_api_integration.htm&type=5). 
In Salesforce, [a connected app](https://help.salesforce.com/s/articleView?id=sf.connected_app_overview.htm) is a framework that authorizes external applications, like Amazon EventBridge connection, to access your Salesforce data.

Identify which OAuth 2.0 authentication mechanism is the most suitable for your use case: 
* [Client credentials](https://help.salesforce.com/s/articleView?id=sf.connected_app_client_credentials_setup.htm)
* [Authorization code](https://help.salesforce.com/s/articleView?id=sf.remoteaccess_oauth_web_server_flow.htm)
* [JWT](https://help.salesforce.com/s/articleView?id=sf.remoteaccess_oauth_jwt_flow.htm)

![Salesforce connected app configuration](https://github.com/Natallia-Bahlai/aws-salesforce-integrations/blob/f615558783016771538f21ef7c1748359c597c89/salesforce-connected-app.png)

## Amazon AppFlow
![AppFlow integration with Salesforce](https://github.com/Natallia-Bahlai/aws-salesforce-integrations/blob/948360e96315d713438edc7df11d519f38351911/Salesforce-%20AppFlow.png)

1. Generate 'Initial Access Token' in the Salesforce connected app 
2. Obtain accessToken and refreshToken
3. Deploy CloudFormation template SFDCStackAppFlow.yaml
4. Perform Account changes in Salesforce and review results in Amazon Athena

## AWS Step Functions
![AppFlow integration with Salesforce](https://github.com/Natallia-Bahlai/aws-salesforce-integrations/blob/948360e96315d713438edc7df11d519f38351911/Salesforce-StepFunc.png)

1. Copy clientId and clientSecret from the Salesforce connected app with client credentials flow
2. Deploy CloudFormation template SFDCStackStepFunction.yaml
3. Execute AWS Step Function

## AWS Event-driven integration using HTTP Proxy with Amazon Kinesis Data Streams
![EDA with Salesforce sending data to Amazon Kinesis Proxy](https://github.com/Natallia-Bahlai/aws-salesforce-integrations/blob/86ac743dec2c794c0ae6dd3c9e79890a179d962d/Salesforce-KDS-Proxy.png)

1. Deploy CloudFormation template SFDCStackKDSProxy.yaml
2. Invoke the Amazon Kinesis Proxy signing request using AWS Signature Version 4 (SignV4). You can run it from Postman selecting AWS signature in Authorization and specifying the AccessKey, SecretKey, Session Token:
```
 curl --location 'https://{api-id}.execute-api.{region}.amazonaws.com/event' \
--header 'Content-Type: application/x-amz-json-1.1' \
--header 'X-Amz-Content-Sha256: ••••••' \
--header 'X-Amz-Security-Token: ••••••' \
--header 'X-Amz-Date: ••••••' \
--header 'Authorization: ••••••' \
--data '{
  "StreamName": "sfdc-kds",
  "Data": "{data}",
  "PartitionKey": "{partition-key}"
}'
```
