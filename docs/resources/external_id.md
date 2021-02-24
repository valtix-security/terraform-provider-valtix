# valtix_external_id

Define an external_id that can be used in an IAM Role Trust Policy to designate who can assume that role. This external_id is also used during the AWS Service Cloud Account setup, along with the IAM Role.

## Example Usage

```hcl
resource "valtix_external_id" extID1 {
    name = "extID1"
}
```

## Argument Reference

* `name` - (Required) Name of the External ID
