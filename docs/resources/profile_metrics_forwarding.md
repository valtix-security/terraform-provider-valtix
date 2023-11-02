# valtix_profile_metrics_forwarding
Resource for creating and managing a Metrics Forwarding Profile

## Example Usage

### Datadog
```hcl
resource "valtix_profile_metrics_forwarding" "datadog" {
	name        = "datadog"
	destination = "DATADOG"
	endpoint    = "https://http-intake.logs.datadoghq.com/"
	auth_token  = "<auth token>"
}
```

### Group
```hcl
resource "valtix_profile_metrics_forwarding" "mf_group" {
  name = "mf-group"
  type = "GROUP"
  group_member_ids = [
    valtix_profile_metrics_forwarding.datadog.id,
  ]
}
```

## Argument Reference

### Common Arguments
* `name` - (Required) Name of the Metrics Forwarding profile
* `destination` - (Required) Supported destination is `DATADOG`.  More destinations will be made available in future releases.
* `type` - (Optional) Specifies whether the Metrics Forwarding Profile is a Group Metrics Forwarding Profile.  Applicable values are `GROUP` or `DEFAULT`.  If not specified or a value of `DEFAULT` is specified, the Metrics Forwarding Profile operates as a Standalone (non-Group) Metrics Forwarding Profile.
* `group_member_ids` - (Required - Group). Ordered list of Metrics Forwarding Profile (Standalone) IDs that make up the components of the Metrics Forwarding Profile (Group).  This argument only applies when `type` is set to `GROUP`.  The list can contain zero or more IDs and is limited to a maximum of 5 IDs.

### Destination-specific Arguments

### Datadog
* `endpoint` - (Required) HTTPS endpoint URL
* `auth_token` - (Required) HTTPS auth token

## Attribute Reference
* `id` - ID of the Metrics Forwarding Profile resource that can be referenced in other resources (e.g., *valtix_gateway*)

## Import
Metrics Forwarding Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_metrics_forwarding.datadog 10
```