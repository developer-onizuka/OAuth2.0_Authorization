# OAuth2.0_Authorization

# 1. Importance of secure access  
There are so many methoads to access own resouces from public in general. Writing them down with proc and cons.

|  | Method | Proc | Cons | Use Case |
| --- | --- | --- | --- | --- |
| #1 | User's Access Key | - Easy to use. | - Likely to be leaked if it is used in the source code. <br> - If leaked, it can be used by anyone who obtains it, which can potentially compromise your cloud resources and Account itself. | Shouldn't use. |
| #2 | Connection String | - Easy to use. <br> - Safer than User's Access Key. <br> - Define some limitations such as expiration date and IP Address. | - Likely to be leaked if it is used in the source code. | - You may use it for some trustworthy person or for temporary. <br> - SAS in Azure Storage Account <br> - Presigned URL in AWS S3 |
