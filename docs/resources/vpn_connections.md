# Resource: valtix_vpn_connections
Resource for creating and managing a VPN Connections resource

## Example Usage
```hcl
resource "valtix_vpn_connections" "vpn_connections1" {
  connection_set_id = 1
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
* `connection_set_id` - (Required) This is a future capability that will accommodate VPN Connection Sets.  This must be set to a value of `1`.
* `vpn_connection` - (Required) One or more blocks, where each block is a row in the VPN Connections that defines each VPN Connection configuration. Structure [defined below](#vpn_connection).

### VPN Connection
* `name` - (Required) Name of the VPN Connection
* `tunnel_endpoint_left` - (Required) The IP address of the physical tunnel endpoint for the left side (Device 1) of the tunnel.  Valid values are either the name of a Valtix Gateway, which will derive the IP from the public IP assigned to the Gateway datapath interface (the Gateway must be deployed with public IPs and not reside behind a NAT), or the public IP of the remote VPN device.  If the `tunnel_endpoint_left` and `tunnel_endpoint_right` are both specified as Valtix Gateways, then the tunnel will be bi-directional (e.g., only a single VPN Connection block is necessary).  Otherwise the tunnel will be a unidirectional tunnel with the Valtix Gateway as the source.
* `tunnel_address_left` - (Required) The IP address of the virtual tunnel endpoint
* `tunnel_endpoint_right` - (Required) The IP address of the physical tunnel endpoint for the right side of the tunnel.  Valid values are either the name of a Valtix Gateway, which will derive the IP from the public IP assigned to the Gateway datapath interface (the Gateway must be deployed with public IPs and not reside behind a NAT), or the public IP of the remote VPN device.  If the `tunnel_endpoint_left` and `tunnel_endpoint_right` are both specified as Valtix Gateways, then the tunnel will be bi-directional (e.g., only a single VPN Connection block is necessary).  Otherwise the tunnel will be a unidirectional tunnel with the Valtix Gateway as a source.
* `tunnel_address_right` - (Required) IP address of the virtual tunnel interface of the VPN Connection endpoint for destination of the VPN Connection (Device 2)
* `pre_shared_key` - (Required) Pre-shared key to use for encryption
* `ipsec_profile` - (Required) ID of the IP Security (IPSec) Profile resource. (e.g., *valtix_profile_ipsec.ipsec1.id*).

## Attribute Reference
* `id` - ID of the VPN Connections resource

## Import
VPN Connections resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_vpn_connections.vpn_connections1 10
```