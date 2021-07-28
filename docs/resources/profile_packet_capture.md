# Resource: valtix_profile_packet_capture

Storage location to store the pcap files

## Example Usage

### AWS

```hcl
resource "valtix_profile_packet_capture" awspcap1 {
  name                 = "awspcap1"
  description          = "pcap description"
  csp_account          = "csp account name added via valtix_cloud_account"
  storage_upload_path  = "s3-bucket-name"
}
```

### Azure

```hcl
resource "valtix_profile_packet_capture" azurepcap1 {
  name                 = "azurepcap1"
  description          = "pcap description"
  csp_account          = "csp account name added via valtix_cloud_account"
  azure_storage_accnt_name = "storage1"
  azure_blob_container_name = "ctr1"
  azure_storage_access_key = "key1"
}
```


### GCP

```hcl
resource "valtix_profile_packet_capture" gcppcap1 {
  name                 = "gcppcap1"
  description          = "pcap description"
  csp_account          = "csp account name added via valtix_cloud_account"
  storage_upload_path  = "gcp-storage-bucket-name"
}
```

## Argument Reference

* `name` - (Required) Name of the IPS profile
* `description` - (Optional) Description of the IPS profile
* `csp_account` - (Required) Cloud account name added to the Controller (valtix_cloud_account.name)
* `storage_upload_path` - (Required AWS and GCP) Storage bucket name
* `azure_storage_accnt_name` - (Required - Azure) Storage account name
* `azure_blob_container_name` - (Required - Azure) Storage container name
* `azure_storage_access_key` - (Optional - Azure) Storage account access key, if required
