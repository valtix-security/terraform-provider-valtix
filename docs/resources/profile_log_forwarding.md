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
* `destination` - (Required) One of `REMOTE_SYSLOG`, `SPLUNK`, `DATADOG`, `GCPLOGGING_FROM_GATEWAY`, `SUMO_LOGIC`, `AWS_S3`, `MS_SENTINEL`
* `siem_vendor` - (Deprecated) One of `REMOTE_SYSLOG`, `SPLUNK`, `DATADOG`, `GCPLOGGING_FROM_GATEWAY`
* `type` - (Optional) Specifies whether the Log Forwarding Profile is a Group Log Forwarding Profile.  The only applicable value is `GROUP`.  If not specified, the Log Forwarding Profile operates as a Standalone (non-Group) Log Forwarding Profile.
* `group_member_ids` - (Required - Group). Ordered list of Log Forwarding Profile (Standalone) IDs that make up the components of the Log Forwarding Profile (Group).  This argument only applies when `type` is set to `GROUP`.  The list can contain zero or more IDs and is limited to a maximum of 5 IDs.

### Destination-specific Arguments

### Splunk
* `endpoint` - (Required) HTTPS endpoint URL
    * For splunk, the most common endpoint is `https://ip-or-fqdn:8088/services/collector`
* `auth_token` - (Required) HTTPS auth token
* `splunk_index` - (Required) Index name where the logs will be stored

### Syslog
* `syslog_server_ip` - (Required) Syslog Server IP
* `syslog_port` - (Required) Syslog Server port
* `syslog_flow_logs` - (Optional) `true` or `false`. Forward Flow Log events.
* `syslog_firewall_events` - (Optional) `true` or `false`. Forward L4 Firewall (L4_FW) events.
* `syslog_https_logs` - (Optional) `true` or `false`. Forward TLS Log/Error (TLS_LOG, TLS_ERROR) events.
* `network_threat_severity` - (Optional) One of `Alert`, `Emergency`, `Critical`, `Error`, `Warning`, `Notice`, `Info`, or `Debug`. Forward Network Threat (IPS) events with the given severity only.
* `web_attack_severity` - (Optional) One of `Alert`, `Emergency`, `Critical`, `Error`, `Warning`, `Notice`, `Info`, `Debug`. Forward Web Attack (WAF) events with the given severity only.

### Datadog
* `endpoint` - (Required) HTTPS endpoint URL
* `auth_token` - (Required) HTTPS auth token

### GCP Logging
* `log_name` - (Optional) GCP Logging name where the logs will be stored. If not specified, the default name is `valtix-gateway-logs`.

### Sumo Logic
* `endpoint` - (Required) Collector endpoint URL

### AWS S3 Bucket
* `csp_account_name` - (Required) Friendly name of the onboarded AWS account where the S3 Bucket resides
* `bucket_name` - (Required) Globally unique name of the S3 Bucket

### MS Sentinel
* `log_analytics_log_type` - (Required) Name of the MS Sentinel table used to store the logs 
* `log_analytics_workspace_id ` - (Required) ID of the MS Sentinel workspace
* `auth_token` - (Required) Shared key / primary key used to authenticate with MS Sentinel

## Attribute Reference
* `id` - ID of the Log Forwarding Profile resource that can be referenced in other resources (e.g., *valtix_gateway*)
* `profile_id` - (Deprecated) Same as the `id` attribute