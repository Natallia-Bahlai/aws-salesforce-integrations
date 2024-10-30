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
![AppFlow integration with Salesforce](https://github.com/Natallia-Bahlai/aws-salesforce-integrations/blob/52b4bcda1963cf8a24e902afba3467ef53b4dd83/Salesforce-AppFlow.png)

1. Generate 'Initial Access Token' in the Salesforce connected app 
2. Obtain accessToken and refreshToken
3. Deploy CloudFormation template /cloudformation/StackAppFlowToS3.yaml or /cloudformation/StackAppFlowToEventBridge.yaml
4. Perform Account changes in Salesforce and review AppFlow Run history
5. Review results in Amazon Athena by running:
```sql
SELECT * FROM "salesforce"."tbl_appflow_accountssyncsfdcs3_sv1" limit 10;
SELECT * FROM "salesforce"."tbl_appflow_accountssyncsfdcs3_{execution_id}_latest" limit 10;
```

## AWS Step Functions
![AppFlow integration with Salesforce](https://github.com/Natallia-Bahlai/aws-salesforce-integrations/blob/948360e96315d713438edc7df11d519f38351911/Salesforce-StepFunc.png)

1. Copy clientId and clientSecret from the Salesforce connected app with client credentials flow
2. Deploy CloudFormation template /cloudformation/StackStepFunction.yaml
3. Execute AWS Step Function and review the result csv in sfdc-stepfunc-{accountId} bucket

## AWS Event-driven integration using HTTP Proxy with Amazon Kinesis Data Streams
![EDA with Salesforce sending data to Amazon Kinesis Proxy](https://github.com/Natallia-Bahlai/aws-salesforce-integrations/blob/86ac743dec2c794c0ae6dd3c9e79890a179d962d/Salesforce-KDS-Proxy.png)

1. Deploy CloudFormation template /cloudformation/StackKDSProxy.yaml
2. Invoke the Amazon Kinesis Proxy signing request using AWS Signature Version 4 (SigV4). You can test it from Postman selecting AWS signature in Authorization and specifying the AccessKey, SecretKey, Session Token:
```http
POST /event HTTP/1.1
Host: {api-id}.execute-api.{region}.amazonaws.com
Content-Type: application/x-amz-json-1.1
Authorization: ••••••
Content-Length: 87

{
  "StreamName": "sfdc-kds",
  "Data": "test",
  "PartitionKey": "sfdc"
}
```
Follow [this guide](https://help.salesforce.com/s/articleView?id=sf.nc_create_edit_awssig4_ext_cred.htm&language=en_US) to apply SigV4 from Salesforce side for HTTP callouts.

## AWS Event-driven integration using Amazon EventBridge to sync Account and Contact data via Salesforce Composite API
![EDA via Amazon EventBridge](https://github.com/Natallia-Bahlai/aws-salesforce-integrations/blob/71d987e70dc7c501ff4d4ea1778ccabef6ec3e4b/Salesforce-EventBridge.png)
1. Deploy CloudFormation template /cloudformation/StackEventBridge.yaml
2. Go to Amazon EventBridge Event Bus, select default Event Bus and click Send events. Provide the following values:
```
Event source: com.src.test
Detail type: accountsync
Event detail: {
    "accountName": "New A",
    "contactName": "New C"
}
```
3. Review created Account & Contact records 
