# valtix_profile_log_forwarding
Resource for creating and managing a Log Forwarding Profile

## Example Usage

### Splunk
```hcl
resource "valtix_profile_log_forwarding" "splunk" {
  name                    = "splunk"
  destination             = "SPLUNK"
  endpoint                = "https://1.2.3.4:8088/services/collector"
  auth_token              = "<auth token>"
  splunk_index            = "main"
}
```

### Syslog
```hcl
resource "valtix_profile_log_forwarding" "syslog" {
  name                    = "syslog"
  destination             = "REMOTE_SYSLOG"
  syslog_server_ip        = "1.2.3.4"
  syslog_port             = "90"
  syslog_flow_logs        = true
  syslog_https_logs       = true
  network_threat_severity = "Alert"
  web_attack_severity     = "Alert"
}
```

### Datadog
```hcl
resource "valtix_profile_log_forwarding" "datadog" {
  name        = "datadog"
  destination = "DATADOG"
  endpoint    = "https://http-intake.logs.datadoghq.com/"
  auth_token  = "<auth token>"
}
```

### GCP Logging
```hcl
resource "valtix_profile_log_forwarding" "gcplogging" {
  name        = "gcplogging"
  destination = "GCPLOGGING_FROM_GATEWAY"
  log_name    = "gcp-logging"
}
```

### Sumo Logic
```hcl
resource "valtix_profile_log_forwarding" "sumo_logic" {
  name        = "sumo_logic"
  destination = "SUMO_LOGIC"
  endpoint    = "https://collectors.sumologic.com/receiver/v1/http/ZaVnC4dhaV39Tn37"
}
```

### AWS S3 Bucket
```hcl
resource "valtix_profile_log_forwarding" "s3bucket" {
  name             = "awss3bucket"
  destination      = "AWS_S3"
  csp_account_name = "aws-account"
  bucket_name      = "aws-s3-bucket-logging"
}
```

### MS Sentinel
```hcl
resource "valtix_profile_log_forwarding" "mssentinel" {
  name                       = "mssentinel"
  destination                = "MS_SENTINEL"
  log_analytics_log_type     = "mssentinel-valtix-logging"
  log_analytics_workspace_id = "bbb7ee6f-6cd4-43e4-a2ab-e32fb70e401f"
  auth_token                 = "<shared-key/primary-key>"
}
```

### Webhook
```hcl
resource "valtix_profile_log_forwarding" "Webhook" {
	name = "Webhook"
	destination = "WEBHOOK"
	endpoint = "https://webhook.example.com"
	auth_type = "AUTH_TYPE_NONE"
}
```

### Group
```hcl
resource "valtix_profile_log_forwarding" "lf_group" {
  name = "lf-group"
  type = "GROUP"
  group_member_ids = [
    valtix_profile_log_forwarding.datadog.id,
    valtix_profile_log_forwarding.mssentinel.id
  ]
}
```

## Argument Reference

### Common Arguments
* `name` - (Required) Name of the Log Forwarding profile
* `destination` - (Required) One of `REMOTE_SYSLOG`, `SPLUNK`, `DATADOG`, `GCPLOGGING_FROM_GATEWAY`, `SUMO_LOGIC`, `AWS_S3`, `MS_SENTINEL`, `WEBHOOK`
* `type` - (Optional) Specifies whether the Log Forwarding Profile is a Standalone or Group Log Forwarding Profile.  Applicable values are `DEFAULT` or `GROUP`.  If not specified, the Log Forwarding Profile operates as a Standalone (non-Group) Log Forwarding Profile (`DEFAULT`).
* `group_member_ids` - (Required - Group). Ordered list of Log Forwarding Profile (Standalone) IDs that make up the components of the Log Forwarding Profile (Group).  This argument only applies when `type` is set to `GROUP`.  The list can contain zero or more IDs and is limited to a maximum of 5 IDs.

### Destination-specific Arguments

### Splunk
* `endpoint` - (Required) Splunk endpoint URL
    * For splunk, the most common endpoint is `https://ip-or-fqdn:8088/services/collector`
* `auth_token` - (Required) Splunk authentication token
* `skip_verify` - (Optional) Skips verifying the issued certificate. Applicable values are `true` or `false`.  If not specified, the default value is `false`.
* `splunk_index` - (Required) Index name where the logs will be stored

### Syslog
* `syslog_server_ip` - (Required) Syslog Server IP
* `syslog_port` - (Required) Syslog Server port
* `skip_verify` - (Optional) Skips verifying the issued certificate. Applicable values are `true` or `false`.  If not specified, the default value is `false`.
* `syslog_flow_logs` - (Optional) `true` or `false`. Forward Flow Log events.
* `syslog_firewall_events` - (Optional) `true` or `false`. Forward L4 Firewall (L4_FW) events.
* `syslog_https_logs` - (Optional) `true` or `false`. Forward TLS Log/Error (TLS_LOG, TLS_ERROR) events.
* `network_threat_severity` - (Optional) One of `Alert`, `Emergency`, `Critical`, `Error`, `Warning`, `Notice`, `Info`, or `Debug`. Forward Network Threat (IPS) events with the given severity only.
* `web_attack_severity` - (Optional) One of `Alert`, `Emergency`, `Critical`, `Error`, `Warning`, `Notice`, `Info`, `Debug`. Forward Web Attack (WAF) events with the given severity only.

### Datadog
* `endpoint` - (Required) Datadog endpoint URL
* `auth_token` - (Required) Datadog authentication token
* `skip_verify` - (Optional) Skips verifying the issued certificate. Applicable values are `true` or `false`.  If not specified, the default value is `false`.

### GCP Logging
* `log_name` - (Optional) GCP Logging name where the logs will be stored. If not specified, the default name is `valtix-gateway-logs`.

### Sumo Logic
* `endpoint` - (Required) Sumo Logic endpoint URL

### AWS S3 Bucket
* `csp_account_name` - (Required) Friendly name of the onboarded AWS account where the S3 Bucket resides
* `bucket_name` - (Required) Globally unique name of the S3 Bucket

### MS Sentinel
* `log_analytics_log_type` - (Required) Name of the MS Sentinel table used to store the logs 
* `log_analytics_workspace_id ` - (Required) ID of the MS Sentinel workspace
* `auth_token` - (Required) Shared key / primary key used to authenticate with MS Sentinel

### Webhook
* `endpoint` - (Required) Webhook endpoint URL
* `field_name` - (Optional) Name of the top-level field within the Webhook JSON payload that might be required by the Webhook endpoint for accepting and processing the payload content
* `batch_size` - (Optional) Number of logs/events to batch together within the JSON payload POST command sent to the Webhook endpoint.  A batch POST will be sent when the `batch_size` threshold is reached or after 10 seconds, whichever occurs first. Applicable values are integers from `1` to `1000` (inclusive).  If not specified, the default value is `100`.  A value of `1` signifies no batching.
* `auth_type` - (Optional) Specifies the authentication method to use when a POST containing the logs/events is submitted to the URL specified in `endpoint`.  Applicable values are `NONE`, `BASIC` or `BEARER_TOKEN`.  When not specified, the default value is `NONE`.
* `username` - (Required) The name of the Webhook user account used for basic authentication. This argument is applicable and required when the `auth_type` is set to `BASIC`. 
* `auth_token` - (Required) The password of the Webhook user account when `auth_type` is set to `BASIC` or the token when `auth_type` is set to `BEARER_TOKEN`. This argument is applicable and required when the `auth_type` is set to `BASIC` or `BEARER_TOKEN`.

## Attribute Reference
* `id` - ID of the Log Forwarding Profile resource that can be referenced in other resources (e.g., *valtix_gateway*)

## Import
Log Forwarding Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_log_forwarding.splunk 10
```