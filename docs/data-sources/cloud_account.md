# DataSource: valtix_cloud_account
Data source for obtaining attributes of a Cloud Account resource

## Example Usage
```hcl
data "valtix_cloud_account" "aws1" {
  name = "aws-account1"
}
```

## Argument Reference
* `name` - (Required) Name of the Cloud Account resource

## Attributes Reference
* `id` - ID of the Cloud Account resource