Using STS to Assume a Role
- Define an IAM role within your account or cross-acount
- Define which principals can access this IAM role
- Use AWS STS (Security Token Service) to retreve credentials and impersonate IAM Role you have access to
- Temporary credentials can be valid between 15 minutes to 1 hour
- You can grant your IAM users permission to switch to roles within your AWS account or to roles defined in other AWS accounts that you own
- Benefits:
  - You must explicitly grant your users permission to assume the role
  - Your users must actively switch to the role using the AWS Management Console or assume the role using AWS CLI or AWS API
  - You can add multi-factor authentication (MFA) protection to the role so that only users who sign in with an MFA device can assume the role
  - Least privilege + auditing using CloudTrail

## Cross Account Access with STS
1. Admin creates role that grant Development account read/write access to an S3 bucket
2. Admin grant members of the gropu Developers permission to assume the role in Production account
3. Users requests Access to role
4 STS returns Role credentials
5. User can access the S3 bucket by using the role credentials

## Providing Access to AWS Accounts owned by third parties
- Zone of trust = accounts, organizations that you own
- Outside Zone of Trust = 3rd parties
- Use IAM Access Analyzer to find out which resources are exposed
- For granting access to a 3rd party:
  - account id
  - an external id
    - To uniquely associate with the role between you and 3rd party
    - Must be provided when defining the trust and when assuming the role
    - Must be chosen by the 3rd party
  - define permissions in the IAM policy

## The Confused Deputy
- When assuming a role, external ID is pass to prevents another AWS customer from accessing the account

## STS Important APIs
- AssumeRole: access a role within your account or cross-account
- AssumeRoleWithSAML: return credentials for users logged with SAML
- AssumeRoleWithWebIdentity: return creds for users logged iwth an IdP:
  - Example providers include Cognito, Login with Amazon, FB, Google, or any OpenID Connect compatible identity provider