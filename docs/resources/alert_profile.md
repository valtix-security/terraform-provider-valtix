# valtix_alert_profile
Configure an Alert Profile that defines the destination where the alerts are sent (Pagerduty, Slack, ServiceNow)

## Example Usage

### Slack
```hcl
resource "valtix_alert_profile" "slack1" {
  name            = "slack1"
  type            = "SlackWebHook"
  integration_url = "https://slack-webhook-url"
}
```

### Pagerduty
```hcl
resource "valtix_alert_profile" "pd1" {
  name            = "pd1"
  type            = "PagerDutyEventApi"
  integration_key = "key"
}
```

### ServiceNow
```hcl
resource "valtix_alert_profile" "sn1" {
  name            = "sn1"
  type            = "ServiceNowWebHook"
  integration_url = "https://service-now-url"
  integration_key = "key"
}
```

## Argument Reference
* `name` - (Required) Name of the alert profile
* `description` - Description
* `type` - (Required) One of `SlackWebHook`, `PagerDutyEventApi`, or `ServiceNowWebHook`
* `integration_key` - (Required for ServiceNow and Pagerduty) Key
* `integration_url` - (Required for ServiceNow and Slack) Webhook URL or ServiceNow Url

## Attribute Reference
* `profile_id` - ID of the Profile that can be referenced in other resources