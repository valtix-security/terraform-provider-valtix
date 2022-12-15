# DataSource: valtix_profile_malicious_ip
Data source for obtaining attributes of a Malicious IP Profile resource.  The attributes can be used in the arguments of a Policy Rules resource.

## Example Usage
```hcl
data "valtix_profile_malicious_ip" "mips1" {
  name = "mips1"
}

resource "valtix_policy_rules" "egress-policy-rules" {
	rule_set_id = valtix_policy_rule_set.egress_policy.id
	rule {
		name                 = "any-to-any"
		action               = "Allow Log"
		state                = "ENABLED"
		service              = valtix_service_object.tcp_443.id
		type                 = "ForwardProxy"
    malicious_ip_profile = data.valtix_profile_malicious_ip.mips1.id
	}
}
```

## Argument Reference
* `name` - (Required) Name of the Malicious IP Profile resource

## Attributes Reference
* `id` - ID of the Malicious IP Profile resource