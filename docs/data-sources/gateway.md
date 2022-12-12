# DataSource: valtix_gateway
Data source for obtaining attributes of a Gateway resource

## Example Usage
```hcl
data "valtix_gateway" "aws-gw1" {
  name = "aws-gw1"
}
```

## Argument Reference
* `name` - (Required) Name of the Gateway resource

## Attributes Reference
* `id` - ID of the Gateway resource