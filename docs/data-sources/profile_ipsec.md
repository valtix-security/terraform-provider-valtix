# DataSource: valtix_profile_ipsec
Data source for obtaining attributes of an IP Security (IPSec) Profile resource.  The attributes can be used in the arguments of a VPN Connections resource.

## Example Usage
```hcl
data "valtix_profile_ipsec" "ipsec1" {
  name = "ipsec1"
}

resource "valtix_vpn_connections" "vpn_connections1" {
	connection_set_id = 1
	vpn_connection {
		# Other arguments hidden for brevity
		ipsec_profile = data.valtix_profile_ipsec.ipsec1.id
	}
}
```

## Argument Reference
* `name` - (Required) Name of the IP Security (IPSec) Profile resource

## Attributes Reference
* `id` - ID of the IP Security (IPSec) Profile resource