# valtix_alert_rule
Resource for creating and managing the Rules that defines the scope of Valtix Alerts to send to a 3rd-party SIEM (Pagerduty, ServiceNow, Slack, Datadog, Microsoft Sentinel).

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
  type          = "Type_AuditLogs"
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
* `description` - Description of the Alert Profile
* `alert_profile` - (Required) Alert Profile ID
* `type` - (Required) Valid values are `Type_Inventory`, `Type_SystemLogs`, or `Type_AuditLogs`
* `sub_type` - (Required - SystemLogs) Applicable only to `type` set to `Type_Inventory` or `Type_SystemLogs`.  For `type` set to `Type_Inventory`, valid value for `sub_type` is `SubType_InventoryGuardRails`.  For `type` set to `Type_SystemLogs`, valid values for `sub_type` are `SubType_SystemLogsGateway` or `SubType_SystemLogsAccount`.
* `severity` - (Required)  Applicable values when `type` is set to `Type_Inventory` are `Info`, `Medium`, or `High`.  Applicable values when `type` is set to `Type_SystemLogs` are `Info`, `Warning`, `Medium`, `High`, or `Critical`.  Alerts will be sent for all alerts that have severity equal to and higher than the specified value.
* `is_active` - (Required) Applicable values are `true` or `false`

## Import
Alert Rule resources can be imported using the resource `name`:

```hcl
$ terraform import valtix_alert_rule.alert_rule1 alert-rule1
```