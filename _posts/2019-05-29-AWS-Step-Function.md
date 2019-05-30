
## 1-hour Timer
When a request is submitted to S3, we were asked to not take any action for 1 hour, in case user changes their mind, and only then proceed with processing the request.

Using Cloudwatch monitoring on the S3 bucket, we fired a step function that waited 1 hour before kicking off processing using Lambda.
