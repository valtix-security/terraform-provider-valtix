# DataSource: valtix_certificate
Data source for obtaining attributes of a Certificate Profile resource

## Example Usage
```hcl
data "valtix_certificate" "import_certificate" {
  name = "import_certificate"
}
```

## Argument Reference
* `name` - (Required) Name of the Certificate Profile resource

## Attributes Reference
* `id` - ID of the Certificate Profile resource