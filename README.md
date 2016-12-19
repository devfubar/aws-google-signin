# aws-google-signin
## Whats the point of this?
The purpose of this repo is to have a minimal example on how to get users caling authorised [AWS API Gateway](https://aws.amazon.com/api-gateway/) endpoints by signing into their google account.
## Set up
### Google
First we need to create/get a Google client ID.  Please follow the google docs on how to do this; [click here](https://developers.google.com/identity/sign-in/web/devconsole-project)
### AWS IAM
We need to create an AWS IAM role so that we can generate a temporary AWS secret key, id and token.

1. Log into AWS console
1. Go to the Roles section of the console and then choose Create New Role.
1. Type a name for the new role that helps you keep track of its use, such as facebookIdentity, and then choose Next Step.
1. In Select Role Type, choose Role for Identity Provider Access.
1. For Grant access to web identity providers, choose Select.
1. From the Identity Provider list, choose 'google'.
1. Enter the google client ID into audience (the one we created in the above section titled 'google')
1. Configure permissions for the resources you want to expose, allowing access to specific operations on specific resources. For more information about IAM permissions, see Overview of AWS IAM Permissions in the IAM User Guide. Review and, if needed, customize the role's trust relationship, and then choose Next Step.
1. Attach additional policies you need and then choose Next Step. For more information about IAM policies, see Overview of IAM Policies in the IAM User Guide.
1. Review the new role and then choose Create Role.

Below is a sample of what the trust IAM rule should look like
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "accounts.google.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "accounts.google.com:aud": "<your google client id here>.apps.googleusercontent.com"
        }
      }
    }
  ]
}
```
Below is an example IAM policy teh role needs to execute 'GET' requests on API Gateway
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "execute-api:GET",
                "execute-api:Invoke"
            ],
            "Resource": "*"
        }
    ]
}
```
### Update the html file

1. Look for the meta tag with the name "google-signin-client_id".  Change the content value from an empty string to your google client id
1. In the javascript section change the variable "roleArn" to the Arn of the role you created in the Setup AWS section above.
1. Set the correct AWS Region for teh variable "AWS.config.region"
1. Change "testFunction" to a real value to call one of your endpoints that require authentication
