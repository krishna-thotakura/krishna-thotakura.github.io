
### Dynamically modify s3 logging when using Terraform

When using Terraform, How to disable S3 logging in AWS environment created for running load test but have it enabled in all other AWS environments by default ?

If you are using the same AWS account for multiple environments(dev, test, loadtest, etc), you probably want to enable logging on your s3 buckets by default. But when running loadtests, you dont really need logging and versioning enabled. Here is how you can modify your terraform scripts to enable this dynamic behavior.

This technique can be applied to any blocks that are part of your terraform resources.

```terraform
# s3 bucket for logs
resource "aws_s3_bucket" "log_bucket" {
 bucket = "com.xyz-${var.environment}.logs"
 acl    = "log-delivery-write"
 count = "${var.environment != "loadtest" ? 1 : 0}"
}



# s3 storage for images
resource "aws_s3_bucket" "images" {
 bucket = "com.xyz-${var.environment}.images.storage"
 acl = "private"

 dynamic "logging" {
   for_each = aws_s3_bucket.log_bucket

   content {
      target_bucket = "com.xyz-${var.environment}.logs"
   }
 }
}
```
