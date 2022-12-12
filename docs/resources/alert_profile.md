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
  integration_key = "key"
}
```

### ServiceNow
```hcl
resource "valtix_alert_profile" "servicenow" {
  name            = "servicenow"
  type            = "ServiceNowWebHook"
  integration_url = "https://service-now-url"
  integration_key = "key"
}
```

### MS Sentinel
```hcl
resource "valtix_alert_profile" "mssentinel1" {
  name       = "mssentinel1"
  type       = "MSSentinel"
  endpoint   = "https://http-intake.logs.datadoghq.com/"
  auth_token = "<auth token>"
}
```

## Argument Reference
* `name` - (Required) Name of the Alert Profile
* `description` - Description of the Alert Profile
* `type` - (Required) One of `SlackWebHook`, `PagerDutyEventApi`, `ServiceNowWebHook`, `MSSentinel`
* `integration_key` - (Required - ServiceNow, Pagerduty) Key
* `integration_url` - (Required - ServiceNow, Slack) Webhook URL or ServiceNow URL

## Attribute Reference
* `id` - ID of the Alert Profile resource that can be referenced in other resources (e.g., *valtix_alert_rule*)
* `profile_id` - (Deprecated) Same as the `id` attribute