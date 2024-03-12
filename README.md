# awspage
Project to create a basic AWS page
The inital goal of this project is to be able to create a static web page, that can be editable using the github repo and github pipeline

# Prerequisites

1. Create or have an [AWS account][1]
2. Create an [github repo][2]

# Step-by-Step
1. [Hosting a static website using AWS S3](#hosting-a-static-website-using-aws-s3)
    1. [Create an S3 Bucket on AWS](#create-an-s3-bucket-on-aws)
    2. [Assign permission to host an http website](#assign-permission-to-host-an-http-website)
2. [Create an IAM User](#create-an-iam-user)
    1. [Create an Policy to allow user to S3](#create-an-policy-to-allow-user-to-s3)
    2. [Create an user on IAM](#create-an-user-on-iam)
    3. [Create access key for an user](#create-access-key-for-an-user)
3. [Create the github pipeline](#create-the-github-pipeline)
    1. [Upload AWS keys to github as secrets](#upload-aws-keys-to-github-as-secrets)
    2. [Create the github action](#create-the-github-action)


## Hosting a static website using AWS S3

### Create an S3 Bucket on AWS

Access to the AWS main page, type S3 on the nav tool and click on the button to access to the S3 console


![navToll][image-main-aws]

In the S3 console, click on buckets and then Create Bucket

![createBucjet][create-bucket]

The most important things to configure are

* Bucket Name
* Public Access configuration

![policyBucket][policy-bucket]

For more information about how to create an S3 bucket you can checkout the create [first bucket page on AWS][3]
You can go onto the create IAM User

### Assign permission to host an http website

In the S3 console select the created bucket and properties, scroll down until the hosting a website option is present and edit

![website-policy][website-policy]

In the page choose allow
In type choose hosting a static website
An write down a file for the index an error

![index-error][index-error]

At the bottom of the page select save changes

## Create an IAM User

### Create an Policy to allow user to S3

In the AWS console, type IAM and click on IAM to access the IAM console

![iam-console][iam-console]

Select on policies and create policy

![create-iam-policy][create-iam-policy]

In the specify permissions choose JSON an paste this policy

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "S3UploadObjectToBucket",
			"Effect": "Allow",
			"Action": "s3:PutObject",
			"Resource": "arn:aws:s3:::<YOUR_BUCKET_NAME>/*"
		}
	]
}
```

This policy allow you to upload files to your bucket, but only in your bucket

After this, it requires the policy name, and a description

### Create an user on IAM
In the AWS console, type IAM and click on IAM to access the IAM console

* Select users and create user
* Type an userName
* For the permission and policies select directly put policies, and search for the policy you just have created
* Now review and accept

### Create access key for an user
In the AWS console, type IAM and click on IAM to access the IAM console
Go to the user tab and find the created user
Go to security credencials, and in access keys select create access key

![create-access][create-access]

In the console, choose CLI, scroll down, enable the confirmation button and click on next

![warning-access][warning-access]

After that you will be prompted with your access keys, copy them on a secure file.

For more information about creating an IAM user checkout the AWS [creating an IAM user in your AWS account][4]

## Create the github pipeline

### Upload AWS keys to github as secrets
On the repository page click the settings bar, and go to the security and select secrets and variable, in actions

![secrets-variable][secrets-variable]

click on new repository secret, and create a secret for the access key and the secret key

![repo-secret][repo-secret]

For more information about secrets checkout the [using secrets in github actions][5]

### Create the github action
Click on the action option in the repository view
Then click in set up a workflow yourself

![create-action][create-action]

Script this in the space 

```
name: Upload Website

on:
  push:
    branches:
    - master

jobs:
  deploy-site:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: jakejarvis/s3-sync-action@master
        with:
          args: --delete
        env:
          AWS_S3_BUCKET: <NAME_OF_BUCKET>
          AWS_ACCESS_KEY_ID: ${{ secrets.<NAME_ACCESS_KEY> }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.<NAME_SECRET_KEY> }}
```

For more information about github actions checkout the [actions][6]

For more information about hosting a website on AWS checkout the [hosting a static website using Amazon S3][7]

[1]:https://docs.aws.amazon.com/accounts/latest/reference/manage-acct-creating.html
[2]:https://docs.github.com/en/repositories/creating-and-managing-repositories/quickstart-for-repositories
[3]:https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-bucket.html
[4]:https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html
[5]:https://docs.github.com/es/actions/security-guides/using-secrets-in-github-actions
[6]:https://docs.github.com/es/actions
[7]:https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html
[image-main-aws]:./Images/S3Console.png
[create-bucket]:./Images/CreateBucket.png
[policy-bucket]:./Images/bucketPolicy.png
[website-policy]:./Images/webhost-policy.png
[index-error]:./Images/index-error.png
[iam-console]:./Images/iam-console.png
[create-iam-policy]:./Images/create-iam-policy.png
[create-access]:./Images/create-access-keys.png
[warning-access]:./Images/waring-acces.png
[secrets-variable]:./Images/create-github-secret.png
[repo-secret]:./Images/repo-secret.png
[create-action]:./Images/actions.png