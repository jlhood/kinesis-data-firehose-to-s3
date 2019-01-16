# kinesis-data-firehose-to-s3

![Build Status](https://codebuild.us-east-1.amazonaws.com/badges?uuid=eyJlbmNyeXB0ZWREYXRhIjoiT1RkbnRFY1QrSTJ4aFpGdlZuMzNCL2NycG1tTWZ1endxMHVhYkllZ0NoOVRibFBHY0pVL3hvb2tmQnhqOGNtTWYwN0VsWXdLRVpsTmtwZmJ4SWRBbFBFPSIsIml2UGFyYW1ldGVyU3BlYyI6IjByS0lWYWxhODJhVkNNU0siLCJtYXRlcmlhbFNldFNlcmlhbCI6MX0%3D&branch=master)

This serverless application provides a Kinesis Data Firehose delivery stream pre-configured to write to an S3 bucket. It is useful for use cases like sending JSON event data to an S3 bucket for querying by Athena.

## App Architecture

![App Architecture](https://github.com/jlhood/kinesis-data-firehose-to-s3/raw/master/images/app-architecture.png)

1. The app creates a Kinesis Data Firehose Delivery Stream and, by default, an S3 bucket to stream events to.
1. The app offers a number optional parameters to customize various aspects of the app including
    1. allowing a pre-existing bucket ARN to be specified and used instead of the app creating its own.
    1. specifying S3 key prefixes, compression, and encryption options.
    1. specifying stream buffering hints.
    1. specifying a Lambda function ARN that can be used to transform the data before writing it to S3. For example, you could use this to remove sensitive data to meet compliance regulations.

## Installation Instructions

This app is meant to be used as part of a larger application, so the recommended way to use it is to embed it as a nested app in your serverless application. To do this, visit the [app's page on the AWS Lambda Console](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:277187709615:applications/kinesis-data-firehose-to-s3). Click the "Copy as SAM Resource" button and paste the copied YAML into your SAM template, filling in any required parameters. Alternatively, you can deploy the application into your account directly via the AWS Lambda Console.

## App Parameters

1. `ExistingS3BucketArn` (optional) - By default, the app creates an S3 bucket to store the logs. However, if this value is populated with a bucket ARN, that bucket will be used instead. Use this if you have an existing S3 bucket or need to control more advanced configuration settings on your bucket.
1. `KeyPrefix` (optional) - Prefix used for all files written to S3. Default: kinesis-data-firehose-to-s3/
1. `CompressionFormat` (optional) - Compression format for S3 files (UNCOMPRESSED, GZIP, ZIP, Snappy). Default: UNCOMPRESSED
1. `EnableBucketEncryption` (optional) - Set to `true` to enable server-side encryption on the S3 bucket using the default aws/s3 AWS KMS master key. Default: `false`
1. `BufferingIntervalInSeconds` (optional) - Buffer incoming data for the specified period of time, in seconds, before delivering it to the destination. See https://docs.aws.amazon.com/firehose/latest/APIReference/API_BufferingHints.html for details. Default: 300
1. `BufferingSizeInMBs` (optional) - Buffer incoming data to the specified size, in MBs, before delivering it to the destination. Note, if you specify a DataTransformationLambdaFunctionArn, you should not set this higher than 6. See https://docs.aws.amazon.com/firehose/latest/dev/data-transformation.html for details. Default: 5
1. `DataTransformationLambdaFunctionArn` (optional) - ARN of data transformation Lambda function. This optional parameter allows you to configure a Lambda function to be invoked by Kinesis Firehose to transformation incoming events before they are saved to S3. For example, you could use this to remove sensitive data to meet compliance regulations.

## App Outputs

1. `BucketName` - Data bucket name. Not returned if existing S3 bucket ARN is provided as an input parameter.
1. `BucketArn` - Data bucket ARN. Not returned if existing S3 bucket ARN is provided as an input parameter.
1. `DeliveryStreamName` - Kinesis Data Firehose delivery stream name.
1. `DeliveryStreamArn` - Kinesis Data Firehose delivery stream ARN.

## License Summary

This code is made available under the MIT license. See the LICENSE file.