# valtix_cloud_account_log_profile_association
Resource for associating a Log Forwarding Profile with a CSP Account for forwarding Discovery Logs

~> **Note on valtix_cloud_account_log_profile_association resource creation**
The [`valtix_cloud_account`](../valtix_cloud_account) and [`valtix_profile_log_forwarding`](../valtix_profile_log_forwarding)
resources must be created before the `valtix_cloud_account_log_profile_association` resource can be created

## Example Usage
```hcl
resource "valtix_cloud_account_log_profile_association" "csp_log_association" {
  csp_account_name = valtix_cloud_account.aws_account1.name
  log_profile_id   = valtix_profile_log_forwarding.s3bucket.profile_id
}
```

## Argument Reference
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix
* `log_profile_id` - (Required) The ID of the Log Forwarding Profile