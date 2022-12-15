# DataSource: valtix_profile_fqdn
Data source for obtaining attributes of a FQDN Filtering Profile resource.  The attributes can be used in the arguments of a Policy Rules resource.

## Example Usage
```hcl
data "valtix_profile_fqdn" "fqdn1" {
  name = "fqdn1"
}

resource "valtix_policy_rules" "egress-policy-rules" {
	rule_set_id = valtix_policy_rule_set.egress_policy.id
	rule {
		name                = "any-to-any"
		action              = "Allow Log"
		state               = "ENABLED"
		service             = valtix_service_object.tcp_443.id
		type                = "ForwardProxy"
    fqdn_filter_profile = data.valtix_profile_fqdn.fqdn1.id
	}
}
```

## Argument Reference
* `name` - (Required) Name of the FQDN Filtering Profile resource

## Attributes Reference
* `id` - ID of the FQDN Filtering Profile resource