# valtix_profile_log_forwarding

## Example Usage

# syslog profile
```hcl
resource "valtix_profile_log_forwarding" syslog1 {
  name                    = "syslog1"
  siem_vendor             = "REMOTE_SYSLOG"
  syslog_server_ip        = "0.0.0.0"
  syslog_port             = "90"
  network_threat_severity = "Alert"
  web_attack_severity     = "Alert"
  syslog_flow_logs        = true
  syslog_https_logs       = true
}
```

# splunk
```hcl
resource "valtix_profile_log_forwarding" splunk1 {
  name                    = "splunk1"
  siem_vendor             = "SPLUNK"
  endpoint                = "https://1.2.3.4:8088/services/collector"
  auth_token              = "http-auth-token-from splunk"
  splunk_index            = "main"
  network_threat_severity = "Alert"
  web_attack_severity     = "Alert"
}
```

# datadog
```hcl
resource "valtix_profile_log_forwarding" datadog1 {
  name                    = "datadog1"
  siem_vendor             = "DATADOG"
  endpoint                = "https://http-intake.logs.datadoghq.com/"
  auth_token              = "test"
  network_threat_severity = "Alert"
  web_attack_severity     = "Alert"
}
```

## Argument Reference

* `name` - (Required) Name of the decryption profile
* `siem_vendor` - (Required) One of **SPLUNK**, **DATADOG**, **REMOTE_SYSLOG**

## Additional arguments based on `siem_vendor`

### SPLUNK
* `endpoint` - (Required ) https URL.
    * For splunk, the most common endpoint is `https://ip-or-fqdn:8088/services/collector`
* `auth_token` - (Required ) https auth token
* `splunk_index` - (Required) splunk index name to store the events

### DATADOG
* `endpoint` - (Required ) https URL.
* `auth_token` - (Required) https auth token

### SYSLOG
* `syslog_server_ip` - (Required) syslog server ip
* `syslog_server_port` - (Required) syslog server port
* `network_threat_severity` - (Required) syslog server port
* `syslog_flow_logs` - (Optional) true/false. forward flow logs to syslog
* `syslog_firewall_events` - (Optional) true/false. forward firewall events to syslog
* `syslog_https_logs` - (Optional) true/false. forward tls logs to syslog
* `network_threat_severity` - (Optional) One of "Alert", "Emergency", "Critical", "Error", "Warning", "Notice", "Info", "Debug". Forward network threat events (ips) with the given severity only
* `web_attack_severity` - (Optional) One of "Alert", "Emergency", "Critical", "Error", "Warning", "Notice", "Info", "Debug". Forward web attacks with the given severity only