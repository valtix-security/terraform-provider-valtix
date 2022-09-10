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
  name                    = "datadog"
  destination             = "DATADOG"
  endpoint                = "https://http-intake.logs.datadoghq.com/"
  auth_token              = "<auth token>"
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

### AWS S3 Bucket
```hcl
resource "valtix_profile_log_forwarding" "s3bucket" {
  name             = "awss3bucket"
  destination      = "AWS_S3"
  csp_account_name = "aws-account"
  bucket_name      = "aws-s3-bucket-logging"
}
```

## Argument Reference

### Common Arguments
* `name` - (Required) Name of the Log Forwarding profile
* `destination` - (Required) One of `REMOTE_SYSLOG`, `SPLUNK`, `DATADOG`, `GCPLOGGING_FROM_GATEWAY`, `AWS_S3`
* `siem_vendor` - (Deprecated) One of `REMOTE_SYSLOG`, `SPLUNK`, `DATADOG`, `GCPLOGGING_FROM_GATEWAY`

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
* `log_name` - [Optional] GCP Logging name where the logs will be stored. If not specified, the default name is `valtix-gateway-logs`.

### AWS S3 Bucket
* `csp_account_name` - (Required) The friendly name of the onboarded AWS account where the S3 Bucket resides
* `bucket_name` - (Required) The globally unique name of the S3 Bucket

## Attribute Reference
* `profile_id` - ID of the Log Forwarding Profile that can be referenced in other resources (e.g., *valtix_gateway*)