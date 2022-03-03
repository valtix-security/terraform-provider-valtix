# DataSource: valtix_address_object
Data source for obtaining the ID of an Address Object that can be used as a Source or Destination (Src/Dest) in a Policy Ruleset Rule, or as a Target Backend Address (Reverse Proxy Target) in a Reverse Proxy Service Object.

## Example Usage
```hcl
data "valtix_address_object" "source-ag" {
  name = "source-ag"
}

data "valtix_address_object" "destination-ag" {
  name = "destination-ag"
}

resource "valtix_policy_rules" "ingress_policy_rules" {
  rule_set_id = valtix_policy_rule_set.ingress_policy_rule_set.rule_set_id
  rule {
    name        = "rule1"
    description = "listen port 80 to target port 80 on app1"
    type        = "Forwarding"
    action      = "ALLOW_LOG"
    source      = data.valtix_address_object.source-ag.address_id
    destination = data.valtix_address_object.destination-ag.address_id
    service     = valtix_service_object.app1-svc-http.service_id
    state       = "ENABLED"
  }
  rule {
    name        = "rule2"
    description = "listen port 443 to target port 443 on app1"
    type        = "Forwarding"
    action      = "ALLOW_LOG"
    source      = data.valtix_address_object.source-ag.address_id
    destination = data.valtix_address_object.destination-ag.address_id
    service     = valtix_service_object.app1-svc-https.service_id
    state       = "DISABLED"
  }
}
```

## Argument Reference
* `name` - (Required) Name of the Address Object

## Attributes Reference
* `address_id` - Set to the Terraform Address Object of the found resource