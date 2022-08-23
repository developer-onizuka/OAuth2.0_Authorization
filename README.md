# OAuth2.0_Authorization

# 1. Access cloud resouces from public
There are so many methoads to access own cloud resouces from public in general. Writing them down with proc and cons.

|  | Method | Proc | Cons | Use Case |
| --- | --- | --- | --- | --- |
| #1 | User's Access Key | - Easy to use. | - Likely to be leaked if it is used in the source code. <br> - If leaked, it can be used by anyone who obtains it, which can potentially compromise your cloud resources and Account itself. | - Shouldn't use. <br> - If you want to use it by some reasons, use it thru opaque type. But don't trust it completly. [Multiple malicious Python packages available on the PyPI repository were caught stealing sensitive information like AWS credentials and transmitting it to publicly exposed endpoints accessible by anyone](https://www.bleepingcomputer.com/news/security/pypi-python-packages-caught-sending-stolen-aws-keys-to-unsecured-sites/). You should use web app scanner such as OWASP ZAP during your CI/CD pipeline. |
| #2 | Connection String | - Easy to use. <br> - Safer than User's Access Key. <br> - Define some limitations such as expiration date and IP Address. | - Likely to be leaked if it is used in the source code. <br> - If leaked, it can be used by anyone who obtains it, which can potentially compromise your storage account. But it is safer than Access key because it is a connection string for accessing the resources with constraints such as expiration date and accessible IP address. Best practices for SAS is [here](https://docs.microsoft.com/en-us/learn/modules/configure-storage-security/7-apply-best-practices). | - You may use it for some trustworthy person or for temporary. <br> - You should use web app scanner such as OWASP ZAP during your CI/CD pipeline. <br> - SAS in Azure Storage Account <br> - Presigned URL in AWS S3  <br> - Internal environment access from onprem server to its organization's own cloud resources.|
| #3 | OAuth2.0 | - Doesn't need to use secrets such as Access Keys or connection strings in code. | - Difficult to understand how it works. <br> - You need to write a code with some SDKs to get a token from the authorization server's token endpoint. | - SaaS subscripution for an unspecified number of customers in general. <br> - Service principal in Azure App registration |

# 2. Grant type of OAuth2.0
There are four types of grant in OAuth2.0. <br> - Authorization code <br> - Implicit <br> - Resource owner password credentials <br> - Client credentials<br><br> But I take Client credentials as an example because it is used in Azure's Metadata Service for Managed ID and easy for me to explain.

<br>

Goal:<br>
- App should get a Token from the Token Endpoint of OAuth2.0's Authorization server to access some specific resouces in the cloud.<br>
- In order to get a Token, App has to send the ClientID (and its Client Secret) to the Token Endpoint of OAuth2.0's Authorization server.<br>
- No ClientIDs should be written in the App because malicious hacker can get it easily and the resouces might have unexpected access from someone.<br>
