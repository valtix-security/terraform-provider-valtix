# DataSource: valtix_fqdn_object
Data source for obtaining attributes of a FQDN Object resource.  The attributes can be used in the arguments of a Policy Rules resource.

## Example Usage
```hcl
data "valtix_fqdn_object" "fqdn1" {
  name = "fqdn1"
}

resource "valtix_policy_rules" "egress-policy-rules" {
	rule_set_id = valtix_policy_rule_set.egress_policy.id
	rule {
		name    = "any-to-any"
		type    = "Forwarding"
		state   = "ENABLED"
		action  = "Allow Log"
    fqdn    = data.valtix_fqdn_object.fqdn1.id
		service = valtix_service_object.tcp_443.id
	}
}
```

## Argument Reference
* `name` - (Required) Name of the FQDN Object resource

## Attributes Reference
* `id` - ID of the FQDN Object resource