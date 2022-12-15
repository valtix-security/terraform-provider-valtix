# DataSource: valtix_alert_rule
Data source for obtaining the attributes of an Alert Rule resource. The attributes can be used in the arguments of an Alert Rule resource.

## Example Usage
```hcl
data "valtix_alert_rule" "alert_rule1" {
  name = "alert-rule1"
}
```

## Argument Reference
* `name` - (Required) Name of the Alert Rule resource

## Attributes Reference
* `id` - ID of the Alert Rule resource