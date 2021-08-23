# Introduction
[AWS Lambda Layers](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html) allow you to package and distribute function code dependencies across multiple functions without needing to zip the dependencies in each [function package](https://docs.aws.amazon.com/lambda/latest/dg/python-package.html) you create. Tenable provides a Python library called [pyTenable](https://pytenable.readthedocs.io/) for interacting with the Tenable API. You can use pyTenable to automate your vulnerability management workflows through calls to the Tenable API. Finally, [Github Actions](https://github.com/features/actions) provide a framework for automating build and deployment steps when creating software.

# pyTenable Lambda Layer Github Action
This Github Action is a manually triggered build and deployment of a pyTenable Lambda Layer that gets uploaded to every supported AWS region within your account. 

## Setup 
### Step 1
First, fork this repo into your own Github account and then create a unique AWS S3 bucket in each of the following regions: 
```
ap-northeast-1
ap-northeast-2
ap-south-1
ap-southeast-1
ap-southeast-2
ca-central-1
eu-central-1
eu-north-1
eu-west-1
eu-west-2
eu-west-3
sa-east-1
us-east-1
us-east-2
us-west-1
us-west-2
```

This step is not included in the Github Action because you will only need to create these buckets one time. You can use the following script to automate this step:
```
#!/usr/bin/env bash

BUCKET_PREFIX='your-ll-bucket-name'

REGIONS=(
  ap-northeast-1
  ap-northeast-2
  ap-south-1
  ap-southeast-1
  ap-southeast-2
  ca-central-1
  eu-central-1
  eu-north-1
  eu-west-1
  eu-west-2
  eu-west-3
  sa-east-1
  us-east-1
  us-east-2
  us-west-1
  us-west-2
)

for region in "${REGIONS[@]}"; do
    bucket_name="${BUCKET_PREFIX}-${region}"
    aws --region $region s3 mb "s3://${bucket_name}"
done
```
### Step 2
Navigate to the `Settings` tab within your Github repo and add secrets for your AWS access and secret keys. Create a [repository secret](https://docs.github.com/en/actions/reference/encrypted-secrets) for both AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY. 

![Create a secret](/imgs/secrets.png)

After your secrets are created you should have the following listed:

![Secrets created](/imgs/secrets_complete.png)

The user associated to these keys must have the minimum permissions of `s3:ListBucket`, `s3:PutObject`, and `s3:GetObject` for the buckets you use in this action and `lambda:AddLayerVersionPermission` and `lambda:PublishLayerVersion` to create the Lambda Layers. See [`sample-policy.json`](sample-policy.json) for an example of the IAM policy to attach to your [programmatic AWS user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html).

# Step 3
Navigate to the `Actions` tab within your Github repo and under `Workflows` click on `Publish pyTenable as a Lambda Layer`. Under the `Run workflow` dropdown, you'll need to change the S3 bucket input to the prefix you set in `$BUCKET_PREFIX` in the script in Step 1. For example, if your buckets follow the pattern `pytenable-layers-myuniqueid-us-east-1`, you will use `pytenable-layers-myuniqueid` as the setting for running this workflow.

![Run workflow](/imgs/workflow.png)

# Step 4
If your build and deployment succeed, you'll now have Lambda Layers within multiple regions for use with your AWS Lambda Functions. Use the following command to see all your layers:

`aws lambda list-layers`

You will need the `LayerArn` when using your pyTenable Lambda Layer within your functions.