# valtix_alert_rule
Resource for creating and managing the Rules that defines the scope of Valtix Alerts to send to a 3rd-party SIEM (Pagerduty, Slack, ServiceNow).

## Example Usage
```hcl
resource "valtix_alert_profile" "slack1" {
  name            = "slack1"
  type            = "SlackWebHook"
  integration_url = "https://slack-webhook-url"
}
```

```hcl
resource "valtix_alert_rule" "alert_rule1" {
  name          = "alert-rule1"
  alert_profile = valtix_alert_profile.slack1.id
  type          = "Type_SystemLogs"
  sub_type      = "SubType_SystemLogsGateway"
  severity      = "Info"
  is_active     = true
}
```

```hcl
resource "valtix_alert_rule" "alert_rule2" {
  name          = "alert-rule2"
  alert_profile = valtix_alert_profile.slack1.id
  type          = "Type_SystemLogs"
  sub_type      = "SubType_SystemLogsAccount"
  severity      = "Info"
  is_active     = true
}
```

```hcl
resource "valtix_alert_rule" "alert_rule3" {
  name          = "alert-rule3"
  alert_profile = valtix_alert_profile.slack1.id
  type          = "Type_Inventory"
  sub_type      = "SubType_InventoryGuardRails"
  severity      = "Info"
  is_active     = true
}
```

## Argument Reference
* `name` - (Required) Name of the Alert Profile
* `description` - Description
* `alert_profile` - (Required) Alert Profile ID
* `type` - (Required) One of `Type_Inventory`, `Type_SystemLogs`,
* `sub_type` - (Required) For Type_Inventory, valid sub_type is `SubType_InventoryGuardRails`. For Type_SystemLogs, valid sub_type is `SubType_SystemLogsGateway` or `SubType_SystemLogsAccount`
* `severity` - (Required) One of `Info`, `Medium`, `High`. For SystemLogs `Critical` and `Warning` are other severity types.
* `is_active` - (Required) `true` or `false`