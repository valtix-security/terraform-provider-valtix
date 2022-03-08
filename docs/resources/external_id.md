# valtix_external_id
Resource to create an external_id that can be used in an IAM Role Trust Policy to designate who can assume that role. This external_id is also used during the AWS Service Cloud Account setup, along with the IAM Role.

## Example Usage
```hcl
resource "valtix_external_id" "ext_id" {
  name = "ext_id1"
}
```

## Argument Reference
* `name` - (Required) Name of the External ID

## Attribute Reference
* `external_id` - External ID that must be used in the trust settings of the cross account IAM role