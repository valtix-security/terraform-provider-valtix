# Resource: valtix_profile_diagnostic
Resource for creating and managing a Diagnostic Profile

## Example Usage

### AWS
```hcl
resource "valtix_diagnostic" "awsdiag1" {
  name                = "awsdiag1"
  description         = "diagnostic description"
  csp_account         = "csp account name added via valtix_cloud_account"
  storage_upload_path = "s3-bucket-name"
}
```

### Azure
```hcl
resource "valtix_profile_packet_capture" "azurediag1" {
  name                      = "azurediag1"
  description               = "diagnostic description"
  csp_account               = "csp account name added via valtix_cloud_account"
  azure_storage_accnt_name  = "storage1"
  azure_blob_container_name = "ctr1"
  azure_storage_access_key  = "key1"
}
```

## Argument Reference
* `name` - (Required) Name of the Diagnostic Profile
* `description` - (Optional) Description of the Diagnostic Profile
* `csp_account` - (Required) Cloud account name added to the Controller (valtix_cloud_account.name)
* `storage_upload_path` - (Required - AWS) Storage bucket name
* `azure_storage_accnt_name` - (Required - Azure) Storage account name
* `azure_blob_container_name` - (Required - Azure) Storage container name
* `azure_storage_access_key` - (Optional - Azure) Storage account access key, if required

## Attribute Reference
* `id` - ID of the Diagnostic Profile resource that can be referenced in other resources (e.g., *valtix_gateway*)
* `profile_id` - (Deprecated) Same as the `id` attribute