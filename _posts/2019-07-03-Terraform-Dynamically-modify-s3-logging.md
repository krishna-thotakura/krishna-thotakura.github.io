
### Using Conditional Operator in Terraform to dynamically create resources

When using Terraform, sometimes you want to create a resource based on a condition. 

For example, Let us say you do not want server access logging on your s3 bucket in loadtest environment. This may be an environment that you build out and execute for few hours and then tear down everything. But you need server access logging on your s3 bucket enabled in all other environments, by default to comply with security policies.

You can implement this using conditional operator, count and for_each as shown below.



```terraform
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

# s3 bucket for logs
resource "aws_s3_bucket" "log_bucket" {
 bucket = "com.xyz-${var.environment}.logs"
 acl    = "log-delivery-write"
 count = "${var.environment != "loadtest" ? 1 : 0}"
}

```
