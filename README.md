# OAuth2.0_Authorization

# 1. Importance of secure access  
There are so many methoads to access own cloud resouces from public in general. Writing them down with proc and cons.

|  | Method | Proc | Cons | Use Case |
| --- | --- | --- | --- | --- |
| #1 | User's Access Key | - Easy to use. | - Likely to be leaked if it is used in the source code. <br> - If leaked, it can be used by anyone who obtains it, which can potentially compromise your cloud resources and Account itself. | Shouldn't use. |
| #2 | Connection String | - Easy to use. <br> - Safer than User's Access Key. <br> - Define some limitations such as expiration date and IP Address. | - Likely to be leaked if it is used in the source code. <br> - If leaked, it can be used by anyone who obtains it, which can potentially compromise your storage account. But it is safer than Access key because it is a connection string for accessing the storage account with constraints such as expiration date and accessible IP address. Best practices for SAS is [here](https://docs.microsoft.com/en-us/learn/modules/configure-storage-security/7-apply-best-practices) | - You may use it for some trustworthy person or for temporary. <br> - SAS in Azure Storage Account <br> - Presigned URL in AWS S3 |
| #3 | OAuth2.0 | - User doesn't need to provide secrets such as Access Keys or connection strings for apps. | - Difficult to understand how to work. <br> - You need to write a code with some SDKs to get a token from the authorization server's token endpoint. | - SaaS subscripution for an unspecified number of customers in general. |
