# DataSource: valtix_policy_rule_set
Data source for obtaining attributes of a Policy Ruleset resource.  A Policy Ruleset is a list of Policy Rules that define the segmentation and security policy for protecting traffic.  The Policy Ruleset can be applied to multiple Gateways to accommodate a dynamic multi-cloud policy.

## Example Usage
```hcl
data "valtix_policy_rule_set" "ingress_policy_standalone" {
  name = "ingress_rule_set_standalone"
}

resource "valtix_gateway" "aws_gw1" {
  # other arguments hidden for brevity
  policy_rule_set_id = data.valtix_policy_rule_set.ingress_policy_rule_set.id
}
```

## Argument Reference
* `name` - (Required) Name of the Policy Ruleset resource

## Attributes Reference
* `id` - ID of the Policy Ruleset resource