# valtix_alert_profile
Resource for creating and managing an Alert Profile that defines the configuration required for sending Alerts to 3rd-party SIEMs (Pagerduty, Slack, ServiceNow, Slack, Datadog, MS Sentinel, MS Teams, Webex, Splunk).  The Alert Profile is used in an Alert Rule.

## Example Usage

### Pagerduty
```hcl
resource "valtix_alert_profile" "pagerduty" {
  name            = "pagerduty"
  type            = "PagerDutyEventApi"
  integration_key = "<key>"
}
```

### ServiceNow
```hcl
resource "valtix_alert_profile" "servicenow" {
  name            = "servicenow"
  type            = "ServiceNowWebHook"
  integration_url = "https://<service-now-url>"
  integration_key = "<key>"
}
```

### Slack
```hcl
resource "valtix_alert_profile" "slack" {
  name            = "slack"
  type            = "SlackWebHook"
  integration_url = "https://<slack-webhook-url>"
}
```

### Datadog
```hcl
resource "valtix_alert_profile" "datadog" {
  name            = "datadog"
  type            = "DataDog"
  integration_key = "<auth token>"
}
```

### Microsoft Sentinel
```hcl
resource "valtix_alert_profile" "mssentinel1" {
  name                       = "mssentinel1"
  type                       = "MicrosoftSentinel"
  log_analytics_log_type     = "mssentinel-valtix-alerting"
  log_analytics_workspace_id = "<workspace-id>"
  integration_key            = "<shared-key/primary-key>"
}
```

### Microsoft Teams
```hcl
resource "valtix_alert_profile" "msteams" {
  name            = "msteams"
  type            = "MicrosoftTeams"
  integration_url = "https://<msteams-url>"
}
```

### Webex
```hcl
resource "valtix_alert_profile" "webex" {
  name            = "webex"
  description     = "Webex"
  type            = "WebexWebHook"
  integration_url = "https://<webex-url>"
}
```

### Splunk
```hcl
resource "valtix_alert_profile" "splunk" {
  name            = "splunk"
  type            = "Splunk"
  integration_url = "https://<splunk-url>"
  integration_key = "<shared-key/primary-key>"
}
```

## Argument Reference

### Common Arguments
* `name` - (Required) Name of the Alert Profile
* `description` - Description of the Alert Profile
* `type` - (Required) One of `PagerDutyEventApi`, `ServiceNowWebHook`, `SlackWebHook`, `DataDog`, `MicrosoftSentinel`, `MicrosoftTeams`, `WebexWebHook`, `Splunk`

### Destination-specific Arguments

### Pagerduty
* `integration_key` - (Required) Shared key / primary key used to authenticate with Pagerduty

### ServiceNow
* `integration_url` - (Required) HTTPS endpoint URL
* `integration_key` - (Required) Shared key / primary key used to authenticate with ServiceNow

### Slack
* `integration_url` - (Required) HTTPS endpoint URL

### Datadog
* `integration_key` - (Required) Shared key / primary key used to authenticate with Datadog

### Microsoft Sentinel
* `integration_key` - (Required) Shared key / primary key used to authenticate with MS Sentinel
* `log_analytics_log_type` - (Required) Name of the MS Sentinel table where the Alerts will be stored
* `log_analytics_workspace_id ` - (Required) ID of the MS Sentinel workspace

### Microsoft Teams
* `integration_url` - (Required) HTTPS endpoint URL

### Webex
* `integration_url` - (Required) HTTPS endpoint URL

### Splunk
* `integration_url` - (Required) HTTPS endpoint URL
* `integration_key` - (Required) Shared key / primary key used to authenticate with Splunk
* `splunk_index` - (Optional) Index name where the Alerts will be stored.  If not specified, the default index of `main` will be used.

## Attribute Reference
* `id` - ID of the Alert Profile resource that can be referenced in other resources (e.g., *valtix_alert_rule*)

## Import
Alert Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_alert_profile.slack 10
```