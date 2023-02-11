# valtix_alert_profile
Resource for creating and managing an Alert Profile that defines the 3rd-party SIEM (Pagerduty, Slack, ServiceNow) and configuration required to send Valtix Alerts.  The Alert Profile is used in an Alert Rule.

## Example Usage

### Slack
```hcl
resource "valtix_alert_profile" "slack" {
  name            = "slack"
  type            = "SlackWebHook"
  integration_url = "https://slack-webhook-url"
}
```

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

### MS Sentinel
```hcl
resource "valtix_alert_profile" "mssentinel1" {
  name                       = "mssentinel1"
  log_analytics_log_type     = "mssentinel-valtix-alerting"
  log_analytics_workspace_id = "bbb7ee6f-6cd4-43e4-a2ab-e32fb70e401f"
  integration_key            = "<shared-key/primary-key>"
}
```

## Argument Reference

### Common Arguments
* `name` - (Required) Name of the Alert Profile
* `description` - Description of the Alert Profile
* `type` - (Required) One of `SlackWebHook`, `PagerDutyEventApi`, `ServiceNowWebHook`, `MicrosoftSentinel`
* `integration_key` - (Required - ServiceNow, Pagerduty) Key
* `integration_url` - (Required - ServiceNow, Slack) Webhook URL or ServiceNow URL

### Destination-specific Arguments

### Slack
* `integration_key` - (Required) Shared key / primary key used to authenticate with Slack

### Pagerduty
* `integration_key` - (Required) Shared key / primary key used to authenticate with Pagerduty

### ServiceNow
* `integration_url` - (Required) HTTPS endpoint URL
* `integration_key` - (Required) HTTPS auth token

### MS Sentinel
* `log_analytics_log_type` - (Required) Name of the MS Sentinel table used to store the alerts 
* `log_analytics_workspace_id ` - (Required) ID of the MS Sentinel workspace
* `integration_key` - (Required) Shared key / primary key used to authenticate with MS Sentinel

## Attribute Reference
* `id` - ID of the Alert Profile resource that can be referenced in other resources (e.g., *valtix_alert_rule*)
* `profile_id` - (Deprecated) Same as the `id` attribute

## Import
[*Public Preview*] Alert Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_alert_profile.slack 10
```