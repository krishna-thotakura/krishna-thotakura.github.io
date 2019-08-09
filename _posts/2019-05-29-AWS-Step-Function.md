
## How to easily implement a 1-hour Timer in AWS 

If you are asked to not process an object put into S3 for exactly time specified(for example, 1 hour) and then only process the object, this can be done easily with AWS Step Function.

Using Cloudwatch monitoring on the S3 bucket, fire a step function that waits for specified duration(1 hour) before kicking off processing using Lambda.
