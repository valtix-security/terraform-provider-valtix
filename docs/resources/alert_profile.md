# valtix_alert_profile

Configure a profile that defines the destination where the alerts are sent (pagerduty, slack, servicenow)
Manages certificates that are presented to the end user when they access valtix gw proxy

## Example Usage

** Slack **
```hcl
resource "valtix_alert_profile" slack1 {
  name            = "slack1"
  type            = "SlackWebHook"
  integration_url = "https://slack-webhook-url"
}
```

** Pagerduty **
```hcl
resource "valtix_alert_profile" pd1 {
  name            = "pd1"
  type            = "PagerDutyEventApi"
  integration_key = "key"
}
```

** ServiceNow **
```hcl
resource "valtix_alert_profile" sn1 {
  name            = "sn1"
  type            = "ServiceNowWebHook"
  integration_url = "https://service-now-url"
  integration_key = "key"
}
```

## Argument Reference

* `name` - (Required) Name of the alert profile
* `description` - Description
* `type` - (Required) One of "SlackWebHook", "PagerDutyEventApi", "ServiceNowWebHook"
* `integration_key` - (Required for ServiceNow and Pagerduty) Key
* `integration_url` - (Required for ServiceNow and Slack) Webhook URL or ServiceNow Url