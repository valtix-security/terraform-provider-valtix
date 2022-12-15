# DataSource: valtix_alert_profile
Data source for obtaining the attributes of an Alert Profile resource. The attributes can be used in the arguments of an Alert Rule resource.

## Example Usage
```hcl
data "valtix_alert_profile" "slack1" {
  name = "slack1"
}

resource "valtix_alert_rule" "alert_rule1" {
  name          = "alert_rule1"
  alert_profile = data.valtix_alert_profile.slack1.id
  type          = "Type_SystemLogs"
  sub_type      = "SubType_SystemLogsGateway"
  severity      = "Info"
  is_active     = true
}
```

## Argument Reference
* `name` - (Required) Name of the Alert Profile resource

## Attributes Reference
* `id` - ID of the Alert Profile resource