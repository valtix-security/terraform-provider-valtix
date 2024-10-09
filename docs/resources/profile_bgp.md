# Resource: valtix_profile_bgp
Resource for creating and managing a BGP Profile

## Example Usage
```hcl
resource "valtix_profile_bgp" "bgp1" {
  name = "bgp1"
  local_as = 1234
  neighbor {
    ip_address = "1.2.3.4"
    autonomous_system = 201
  }
}
```

## Argument Reference
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `local_as` - (Required) The local Autonomous System (AS) number for the BGP peer
* `neighbor` - (Required) One or more blocks, where each block is a row in the BGP Profile that defines the BGP neighbor relationship. Structure [defined below](#neighbor).

### Neighbor
* `ip_address` - (Required) The neighbor IP address of the BGP peer
* `autonomous_system` - (Required) The neighbor Autonomous System (AS) number of the BGP peer

## Attribute Reference
* `id` - ID of the BGP Profile resource that can be referenced in other resources (e.g., *valtix_gateway*)

## Import
BGP Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_bgp.bgp1 10
```