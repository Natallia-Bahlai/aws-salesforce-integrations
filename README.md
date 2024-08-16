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

1. Generate 'Initial Access Token' in the Salesforce connected app 
2. Obtain accessToken and refreshToken
3. Deploy CloudFormation template SFDCStackAppFlow.yaml
4. Perform Account changes in Salesforce and review results in Amazon Athena

## AWS Step Functions

1. Copy clientId and clientSecret from the Salesforce connected app with client credentials flow
2. Deploy CloudFormation template SFDCStackStepFunction.yaml
3. Execute AWS Step Function
