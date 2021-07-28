# valtix_alert_rule

Define the rules for the alerts and the destination alert profile where the alerts are sent

## Example Usage

```hcl
resource "valtix_alert_profile" slack1 {
  name            = "slack1"
  type            = "SlackWebHook"
  integration_url = "https://slack-webhook-url"
}
```

```hcl
resource "valtix_alert_rule" rule1 {
    name = "rule1"
    alert_profile = valtix_alert_profile.slack1.profile_id
    type = "Type_SystemLogs"
    sub_type = "SubType_SystemLogsGateway"
    severity = "Info"
    is_active = true
}
```

```hcl
resource "valtix_alert_rule" rule2 {
    name = "rule2"
    alert_profile = valtix_alert_profile.slack1.profile_id
    type = "Type_SystemLogs"
    sub_type = "SubType_SystemLogsAccount"
    severity = "Info"
    is_active = true
}
```

```hcl
resource "valtix_alert_rule" rule3 {
    name = "rule3"
    alert_profile = valtix_alert_profile.slack1.profile_id
    type = "Type_Inventory"
    sub_type = "SubType_InventoryGuardRails"
    severity = "Info"
    is_active = true
}
```

## Argument Reference

* `name` - (Required) Name of the alert profile
* `description` - Description
* `alert_profile` - (Required) Alert profile id
* `type` - (Required) One of "Type_Inventory", "Type_SystemLogs",
* `sub_type` - (Required) For Type_Inventory, valid sub_type is "SubType_InventoryGuardRails",
    For Type_SystemLogs, valid sub_type is "SubType_SystemLogsGateway" or "SubType_SystemLogsAccount"
* `severity` - (Required) One of "Info", "Medium", "High". For SystemLogs "Critical" and "Warning" are other severity types
* `is_active` - (Required) true or false
