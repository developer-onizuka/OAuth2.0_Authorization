# OAuth2.0_Authorization

# 1. Access cloud resouces from public
There are so many methoads to access own cloud resouces from public in general. Writing them down with pros and cons.
<br>
I belive that the **Signed URL in AWS CloudFront** is one of the solutions for access from plublic, but I will take up it on another occation. <br>

|  | Method | Pros | Cons | Use Case |
| --- | --- | --- | --- | --- |
| #1 | User's Access Key | - Easy to use. | - Likely to be leaked if it is used in the source code. <br> - If leaked, it can be used by anyone who obtains it, which can potentially compromise your cloud resources and Account itself. | - Shouldn't use because Access key is used as direct access to public API of every cloud resouce which you allow to be controlled. <br> - If you want to use it by some reasons, use it thru environment variables or [opaque type](https://github.com/developer-onizuka/mvc_CosmosDB#3-create-a-secret-resouce-in-kubernetes-cluster). But don't trust it completely. ([Multiple malicious Python packages available on the PyPI repository](https://www.bleepingcomputer.com/news/security/pypi-python-packages-caught-sending-stolen-aws-keys-to-unsecured-sites/)) <br> - You should use web app scanner such as OWASP ZAP during your CI/CD pipeline. |
| #2 | Connection String | - Easy to use. <br> - Safer than User's Access Key. <br> - Define some limitations such as expiration date and IP Address. | - Likely to be leaked if it is used in the source code. <br> - If leaked, it can be used by anyone who obtains it, which can potentially compromise your cloud resources. But it is safer than Access key because it is a connection string for accessing the resources with constraints such as expiration date and accessible IP address. Best practices for SAS is [here](https://docs.microsoft.com/en-us/learn/modules/configure-storage-security/7-apply-best-practices). | - You may use it for some trustworthy person or for temporary. <br> - Internal environment access from onprem server to its organization's own cloud resources. <br> - Use it thru environment variables or opaque type. But don't trust it completely. <br> - You should use web app scanner such as OWASP ZAP during your CI/CD pipeline. <br> - SAS in Azure Storage Account <br> - Pre-Signed URL in AWS S3 |
| #3 | Access Token<br>(OAuth2.0) | - Doesn't need to use secrets such as Access Keys or connection strings in code. | - Difficult to understand how it works. <br> - You need to write a code with some SDKs to get a token from the authorization server's token endpoint. <br> - Default token lifetime ranges between 60-90 minutes. Needs logics toward expiration (But it is not necessary on Managed ID). | - Zero Trust <br> - SaaS subscripution for an unspecified number of customers in general. <br> - [Service principal](https://github.com/developer-onizuka/OAuth2.0_AzureAD#oauth20_azuread) in Azure App registration <br> - Cognito User Pools / Cognito User Identity Pools in AWS|

# 2. How OAuth2.0 works
**(1) Goals with OAuth2.0** 
- App should get a Token from the Token Endpoint of OAuth2.0's Authorization server to access some specific resouces in the cloud.<br>
- In order to get a Token, App has to send the ClientID (and its Client Secret) to the Token Endpoint of OAuth2.0's Authorization server.<br>
- No ClientIDs should be written in the App because malicious hacker can get it easily and the resouces might have unexpected access from someone.<br>

By the way, there are four types of grant in OAuth2.0. 
```
- Authorization code
- Implicit
- Resource owner password credentials 
- Client credentials
```
But I take **Client credentials** as an example because it is used in Azure's Metadata Service for Managed ID and easy for me to explain.

**(2) Between cloud resouces** <br>
You can use Azure's Metadata Service for Managed ID while the access is between cloud resouces.<br>
Azure's Managed ID uses OAuth2.0 Client credentials technology as far as I know. The ClientID in metadata service is already registered by system administrator of the Azure subscription. No ClientIDs is written in the App as you can see below: <br>

![metadata_service_Azure_ManagedID.drawio.png](https://github.com/developer-onizuka/OAuth2.0_Authorization/blob/main/metadata_service_Azure_ManagedID.drawio.png)

**(ex) Laundry service in dormitory** <br>
One day, a lazy student in a dormitory uses a housekeeping service to have them wash his dirty clothes. As you can imagine, the service guy does not have any permissions to use washing machine in the dormitory. So the service guy needs to ask dormitory manager a token.<br>
But dormitory manager does not have any tokens in person, neither. Please also note the dormitory manager does not have any knowledges about washing machines. So the dormitory manager asks it for the token manager who manages and maintains washing machines in the dormitory.<br>
Finally, the service guy gets a token and can put it into a washing machine so that he can wash his costomer's dirty clothes. But please note the service guy can not do anything besides using washing machines. This is because the service guy does not have any tokens to use dormitorie's property such as kitchen or bathroom.

![dormitory.drawio.png](https://github.com/developer-onizuka/OAuth2.0_Authorization/blob/main/dormitory.drawio.png)

**(3) From public to cloud resouces via Azure Service Principal** <br>
According to the table in Section 1, OAuth2.0 is the best practice for the accesss from public to cloud resources, such as some specific SaaS solutions. 
But, you can use **Service Principal** in Azure AD instead of Managed ID.<br>
I believe that ClientID should be managed in some reliable Database systems getting along with your App.

![OAuth2.0_ClientCredentialsFlow.drawio.png](https://github.com/developer-onizuka/OAuth2.0_Authorization/blob/main/OAuth2.0_ClientCredentialsFlow.drawio.png)

**(ex) Laundry service in your home** <br>
The lazy student decided to live outside dormitory independently. He starts to subscribe two services.<br> One is for housekeeping and the other is for laundry service because he does not have any washing machines in his house. The housekeeping service can federate with some major subscriptions such as sharing washing machines, cars and buying meats and milk at grocery online store. The laundry service can provide a token for subscribers to allow them to use washing machines. <br>
One day, he uses the housekeeping service to have them wash his dirty clothes again. He wants them to use the laundry service instead of himself. What he wants to do is **"Single Sign On"** between housekeeping service and laundry service. 

![laundryService_SSO.drawio.png](https://github.com/developer-onizuka/OAuth2.0_Authorization/blob/main/laundryService_SSO.drawio.png) <br>

The picture above is based on **OAuth2.0 Client credentials** flow. But if "Authorization code" is used above, **the student will be redirected to login site of the coin laundry service** and asked grant for **the federation the coin laundry service provider with the housekeeping service**.

**(4) From public to cloud resouces via AWS Cognito** <br>
Cognito Identity Pools issues a token for each user as a temporary credential. There are two types of managed identities: Authenticated users and Unauthenticated users (guest users). Each access should be controled by Cognito User Pool's authentication and authorization (permissions to be given for each user) with AssumeRole API. <br>

![AWS_Cognito.drawio.png](https://github.com/developer-onizuka/OAuth2.0_Authorization/blob/main/AWS_Cognito.drawio.png)

<br>

![AWS_Cognito_unauthenticated.drawio.png](https://github.com/developer-onizuka/OAuth2.0_Authorization/blob/main/AWS_Cognito_unauthenticated.drawio.png)


# 3. Summary
- The point is how we should manage a ClientID which is a kind of secrets to get a Token of cloud resouces thru OAuth2.0. <br>
- The Managed ID is one of implementation using OAuth2.0 and it can be used in Azure while IAM role is used in AWS. <br>
- As it turns out, Managed ID in Azure is a kind of system to manage the ClientID securely thru REST API, I believe. <br>
- In the on-prem enviornment, the ClientID would be managed in some reliable Database systems getting along with your App instead of Managed ID in cloud. <br>

|  | to On-prem's resource | to Cloud's resource |
| --- | --- | --- |
| **from on-premises** <br> (within the organization)| Connection String | Connection String |
| **from the resources in same cloud subscription** <br> (within the organization)| N/A | OAuth2.0 (Cloud Service Providers provide you services below) <br> - **AWS IAM role** <br> - **Azure ManagedID** |
| **from different cloud subscriptions** <br> (within the organization)| Connection String | - [Cross Account Access between AWS accounts](https://n2ws.com/blog/aws-cloud/managing-aws-accounts-cross-account-iam-roles) (but shoud be trusted between AWS accounts) <br> - [Access with Managed ID between Azure subscriptions which each belongs to the same Azure AD tenant.](https://stackoverflow.com/questions/59069065/can-managed-identity-of-a-azure-function-have-access-across-multiple-subscriptio) (But [Managed IDs don't currently support cross-tenant scenarios.](https://learn.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/managed-identities-faq#can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant)) <br> - [Azure AD External Identities](https://www.youtube.com/watch?v=3Pi_-pHIT_4) for B2B connection. Cross-tenant access setting manages Inbound and Outband traffics for user principals (not for Managed ID principals).<br> - [Authentication flow for external Azure AD users](https://learn.microsoft.com/en-us/azure/active-directory/external-identities/authentication-conditional-access): <br> The most important step is "**to see if the resource tenant trusts MFA and device claims (device compliance, hybrid Azure AD joined status) from external tenant's users.**" <br> If it is not, then "**B2B collaboration users are prompted for MFA, which they need to satisfy in the resource tenant.**"|
| **from anonymous public resources thru RESTful API / GraphQL API** <br> (outside the organization)| OAuth2.0 (Must make the scheme on your own) | OAuth2.0 (Cloud Service Providers provide you services below) <br> - **AWS Cognito User Pools / AWS Cognito Identity Pools** <br> - **Azure Service Principal** |


