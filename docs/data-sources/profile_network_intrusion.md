# DataSource: valtix_profile_network_intrusion
Data source for obtaining attributes of a Network Intrusion (IDS/IPS) Profile resource.  The attributes can be used in the arguments of a Policy Rules resource.

## Example Usage
```hcl
data "valtix_profile_network_intrusion" "ips1" {
  name = "ips1"
}

resource "valtix_policy_rules" "ingress_policy_rules" {
  rule_set_id = valtix_policy_rule_set.ingress_policy.id
  rule {
    name                      = "tcp-443"
    type                      = "ReverseProxy"
    state                     = "ENABLED"
    action                    = "Allow Log"
    service                   = valtix_service_object.app1-svc-https.id
    network_intrusion_profile = data.valtix_profile_network_intrusion.ips1.id
  }
}
```

## Argument Reference
* `name` - (Required) Name of the Network Intrusion (IDS/IPS) IP Profile resource

## Attributes Reference
* `id` - ID of the Network Intrusion (IDS/IPS) Profile resource