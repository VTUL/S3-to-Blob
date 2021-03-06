# AWS S3 to Azure Blob Data Sync Using ADFv2

## What's S3-to-Blob?
"S3-to-Blob" is a tool for copying objects from an AWS S3 bucket into an Azure Blob Storage container. The solution is built on top of Azure Data Factory (ADF) v2 and utilizes its built-in S3 connector for accessing S3 buckets. However, ADFv2 does not support incremental data copy out of the box. S3-to-Blob extends ADFv2 to support performing incremental copying from the source bucket into the target container so that only deltas are considered and copied over the network between the AWS and Azure data centers.

### Use Cases
1) **One-time Run**: S3-to-Blob can be used as a one-time tool for copying files and other artifacts from S3 to Azure Blob Storage.
2) **Scheduled**: It can be used for monitoring an AWS S3 bucket and keeping track of its contents. This is done by virtue of using ADFv2's scheduled triggers which can be scheduled to run on a reguler interval. Upon detecting a new object being uploaded into the S3 bucket, it will be copied into an Azure Storage account (blob-only or general purpose, v1 or v2).

## Deployment
You can deploy this solution by clicking on the following button:

[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FStratusOn%2FS3-to-Blob%2Fmaster%2Fsrc%2FDeployments%2Fazuredeploy.json)

### Post-Deployment
* Once the deployment finishes (it takes about 2 minutes), go to the resource group, click on the Azure Data Factory resource that was created and then click on "Author & Monitor".
* Click on the "Author" button on the left navigation menu. This loads the editor.
* Expand the "Pipelines" list and click on the "MasterCopyFromAmazonS3ToAzureBlob" pipeline. Click on the "Debug" button to test it.
* Under triggers, locate the "SyncObjectsTrigger" trigger. Edit it and check the "Activated" checkbox (it's unchecked by default). This trigger is configured to run once every 1 hour.

## Quick Demo & Walk-through (length: 00:06:32)

[![ADFv2 S3-to-Blob Data Sync](http://img.youtube.com/vi/Z_zhiB0sTc8/0.jpg)](https://youtu.be/Z_zhiB0sTc8 "ADFv2 S3-to-Blob Data Sync")

## Assumptions
* This assumes that you have access to an S3 account (via a key and a secret) but cannot make midifications to the AWS account like creating an AWS Lambda function to spy on an S3 bucket and trigger the copying to the Azure Storage account.

## S3 Bucket Permissions
The ADF [documentation](https://docs.microsoft.com/en-us/azure/data-factory/connector-amazon-simple-storage-service) specifies the required permissions. The following policy definition can be used as an example:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt00001",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": [
                "arn:aws:s3:::mybucket/*"
            ]
        },
        {
            "Sid": "Stmt00002",
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:ListBucket",
                "s3:ListAllMyBuckets"
            ],
            "Resource": [
                "arn:aws:s3:::*"
            ]
        }
    ]
}
```

## Reference

ADF concepts: https://docs.microsoft.com/en-us/azure/data-factory/concepts-pipelines-activities

Incremental data load: https://docs.microsoft.com/en-us/azure/data-factory/tutorial-incremental-copy-overview
