# DataSource: valtix_profile_bgp
Data source for obtaining attributes of an BGP Profile resource.  The attributes can be used in the arguments of a Gateway resource.

## Example Usage
```hcl
data "valtix_profile_bgp" "bgp1" {
  name = "bgp1"
}

resource "valtix_gateway" "aws_gw1" {
	# Other arguments hidden for brevity
	bgp_profile = data.valtix_bgp.bgp1.id
}
```

## Argument Reference
* `name` - (Required) Name of the BGP Profile resource

## Attributes Reference
* `id` - ID of the BGP Profile resource