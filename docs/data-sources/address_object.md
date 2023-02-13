# DataSource: valtix_address_object
Data source for obtaining the attributes of an Address Object resource. The attributes can be used in a Policy Rules resource for specifying the Source or Destination (Src/Dest) arguments, or as a Target Backend Address (Reverse Proxy Target) argument in a Reverse Proxy Service Object resource.

## Example Usage
```hcl
data "valtix_address_object" "source_ag" {
  name = "source-ag"
}

data "valtix_address_object" "destination_ag" {
  name = "destination-ag"
}

resource "valtix_policy_rules" "ingress_policy_rules" {
  rule_set_id = valtix_policy_rule_set.ingress_policy_rule_set.id
  rule {
    name        = "tcp-443"
    type        = "Forwarding"
    state       = "ENABLED"
    action      = "ALLOW_LOG"
    source      = data.valtix_address_object.source_ag.id
    destination = data.valtix_address_object.destination_ag.id
    service     = valtix_service_object.app1_svc_https.id
  }
}
```

## Argument Reference
* `name` - (Required) Name of the Address Object resource

## Attributes Reference
* `id` - ID of the Address Object resource
* `address_id` - (Deprecated) Same as the `id` attribute