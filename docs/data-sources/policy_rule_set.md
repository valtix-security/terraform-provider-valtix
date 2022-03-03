# DataSource: valtix_policy_rule_set
Data source for obtaining the ID of a Policy Ruleset.  A Policy Ruleset is a list of Policy Rules that define the segmentation and security policy for protecting traffic.  The Policy Ruleset can be applied to multiple Gateways to accommodate a dynamic multi-cloud policy.

## Example Usage
```hcl
data "valtix_policy_rule_set" "ingress_policy_rule_set" {
  name = "ingress_rule_set"
}

resource "valtix_gateway" "aws-gw1" {
  # other arguments hidden for brevity
  policy_rule_set_id = data.valtix_policy_rule_set.ingress_policy_rule_set.rule_set_id
}
```

## Argument Reference
* `name` - (Required) Name of the Policy Ruleset

## Attributes Reference
* `rule_set_id` - Set to the Terraform Policy Ruleset ID of the found resource