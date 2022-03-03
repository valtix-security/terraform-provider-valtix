# valtix_profile_log_forwarding
Create Log Forwarding Profile

## Example Usage

### syslog
```hcl
resource "valtix_profile_log_forwarding" "syslog1" {
  name                    = "syslog1"
  siem_vendor             = "REMOTE_SYSLOG"
  syslog_server_ip        = "0.0.0.0"
  syslog_port             = "90"
  network_threat_severity = "Alert"
  web_attack_severity     = "Alert"
  syslog_flow_logs        = "true"
  syslog_https_logs       = "true"
}
```

### splunk
```hcl
resource "valtix_profile_log_forwarding" "splunk1" {
  name                    = "splunk1"
  siem_vendor             = "SPLUNK"
  endpoint                = "https://1.2.3.4:8088/services/collector"
  auth_token              = "http-auth-token-from splunk"
  splunk_index            = "main"
  network_threat_severity = "Alert"
  web_attack_severity     = "Alert"
}
```

### datadog
```hcl
resource "valtix_profile_log_forwarding" "datadog1" {
  name                    = "datadog1"
  siem_vendor             = "DATADOG"
  endpoint                = "https://http-intake.logs.datadoghq.com/"
  auth_token              = "test"
  network_threat_severity = "Alert"
  web_attack_severity     = "Alert"
}
```

### gcp log-forwarding
```hcl
resource "valtix_profile_log_forwarding" "gcplog1" {
  name        = "gcplog1"
  siem_vendor = "GCPLOGGING_FROM_GATEWAY"
  log_name    = "gcp-logging-1"
}
```

## Argument Reference
* `name` - (Required) Name of the decryption profile
* `siem_vendor` - (Required) One of `SPLUNK`, `DATADOG`, `GCPLOGGING_FROM_GATEWAY`, or `REMOTE_SYSLOG`

## Additional arguments based on `siem_vendor`

### SPLUNK
* `endpoint` - (Required ) HTTPS URL
    * For splunk, the most common endpoint is `https://ip-or-fqdn:8088/services/collector`
* `auth_token` - (Required ) HTTPS auth token
* `splunk_index` - (Required) Splunk index name to store the events

### DATADOG
* `endpoint` - (Required ) HTTPS URL
* `auth_token` - (Required) HTTPS auth token

### GCPLOGGING_FROM_GATEWAY
* `log_name` - [Optional] GCP log name to store the logs. If not specified, the default name is "valtix-gateway-logs"

### SYSLOG
* `syslog_server_ip` - (Required) Syslog Server IP
* `syslog_port` - (Required) Syslog Server port
* `network_threat_severity` - (Required) Syslog Server port
* `syslog_flow_logs` - (Optional) `true` or `false`. Forward flow logs to Syslog.
* `syslog_firewall_events` - (Optional) `true` or `false`. Forward firewall events to Syslog.
* `syslog_https_logs` - (Optional) `true` or `false`. Forward TLS logs to Syslog.
* `network_threat_severity` - (Optional) One of `Alert`, `Emergency`, `Critical`, `Error`, `Warning`, `Notice`, `Info`, or `Debug`. Forward Network Threat events (IPS) with the given severity only.
* `web_attack_severity` - (Optional) One of `Alert`, `Emergency`, `Critical`, `Error`, `Warning`, `Notice`, `Info`, `Debug`. Forward Web Attacks with the given severity only.

## Attribute Reference
* `profile_id` - ID of the Profile that can be referenced in other resources (e.g., *valtix_gateway*)