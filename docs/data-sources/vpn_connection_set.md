# DataSource: valtix_vpn_connection_set
Data source for obtaining attributes of an VPN Connection Set resource.  The attributes can be used in the arguments of a Gateway resource and VPN Connections resource.

## Example Usage
```hcl
data "valtix_vpn_connection_set" "vpn_connection_set1" {
  name = "vpn_connection_set1"
}

resource "valtix_gateway" "aws_gw1" {
  # Other arguments hidden for brevity
  vpn_connection_set_id = data.valtix_vpn_connection_set.vpn_connections1.id
}

resource "valtix_vpn_connections" "vpn_connections1" {
  connection_set_id = data.valtix_vpn_connection_set.vpn_connections1.id
  vpn_connection {
    name = "vpn-connection1"
    tunnel_endpoint_left = "aws-egress-gateway"
    tunnel_address_left = "85.0.0.1"
    tunnel_endpoint_right = "azure-egress-gateway"
    tunnel_address_right = "85.0.0.2"
    pre_shared_key = "abcd1234"
    ipsec_profile = valtix_profile_ipsec.ipsec1.id
  }
  vpn_connection {
    name = "vpn-connection2"
    tunnel_endpoint_left = "aws-egress-gateway" 
    tunnel_address_left = "50.0.0.1"
    tunnel_endpoint_right = ""
    tunnel_address_right = "50.0.0.2"
    pre_shared_key = "abcd1234"
    ipsec_profile = valtix_profile_ipsec.ipsec2.id
  }
}
```

## Argument Reference
* `name` - (Required) Name of the VPN Connection Set resource

## Attributes Reference
* `id` - ID of the VPN Connection Set resource