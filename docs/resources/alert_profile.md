# valtix_alert_profile
Resource for creating and managing an Alert Profile that defines the configuration required for sending Alerts to 3rd-party SIEMs (Pagerduty, Slack, ServiceNow, Slack, Datadog, MS Sentinel, Webex).  The Alert Profile is used in an Alert Rule.

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
  integration_url = "https://service-now-url"
  integration_key = "<key>"
}
```

### Slack
```hcl
resource "valtix_alert_profile" "slack" {
  name            = "slack"
  type            = "SlackWebHook"
  integration_url = "https://slack-webhook-url"
}
```

### Datadog
```hcl
resource "valtix_alert_profile" "datadog" {
  name            = "datadog"
  type            = "DataDog"
  integration_url = "https://http-intake.logs.datadoghq.com/"
  integration_key = "<auth token>"
}
```

### Microsoft Sentinel
```hcl
resource "valtix_alert_profile" "mssentinel1" {
  name                       = "mssentinel1"
  type                       = "MicrosoftSentinel"
  log_analytics_log_type     = "mssentinel-valtix-alerting"
  log_analytics_workspace_id = "bbb7ee6f-6cd4-43e4-a2ab-e32fb70e401f"
  integration_key            = "<shared-key/primary-key>"
}
```

### Webex
```hcl
resource "valtix_alert_profile" "webex" {
  name            = "webex"
  description     = "Webex"
  type            = "WebexWebHook"
  integration_url = "https://webexapis.com/v1/webhooks/incoming/Y2lzY29zcGFyazovL3VzL1dFQkhPT0svYjc5NTQ0NzMtMWQ2ZC00Y2I0LTk1ZWMtYzFlNTA0NGZlNTE2"
}
```

## Argument Reference

### Common Arguments
* `name` - (Required) Name of the Alert Profile
* `description` - Description of the Alert Profile
* `type` - (Required) One of `PagerDutyEventApi`, `ServiceNowWebHook`, `SlackWebHook`, `DataDog`, `MicrosoftSentinel`, `WebexWebHook`

### Destination-specific Arguments

### Pagerduty
* `integration_key` - (Required) Shared key / primary key used to authenticate with Pagerduty

### ServiceNow
* `integration_url` - (Required) HTTPS endpoint URL
* `integration_key` - (Required) Shared key / primary key used to authenticate with ServiceNow

### Slack
* `integration_url` - (Required) HTTPS endpoint URL

### Datadog
* `integration_url` - (Required) HTTPS endpoint URL
* `integration_key` - (Required) Shared key / primary key used to authenticate with Datadog

### Microsoft Sentinel
* `log_analytics_log_type` - (Required) Name of the MS Sentinel table used to store the alerts 
* `log_analytics_workspace_id ` - (Required) ID of the MS Sentinel workspace
* `integration_key` - (Required) Shared key / primary key used to authenticate with MS Sentinel

### Webex
* `integration_url` - (Required) HTTPS endpoint URL

## Attribute Reference
* `id` - ID of the Alert Profile resource that can be referenced in other resources (e.g., *valtix_alert_rule*)
* `profile_id` - (Deprecated) Same as the `id` attribute

## Import
Alert Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_alert_profile.slack 10
```