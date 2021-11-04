# DataSource: valtix_policy_rule_set
A policy rule set is a list of firewall rules. Use this data-source to get the ID of the Policy Rule Set. The rule set can be applied to multiple gateways to achieve identical security posture.

## Example Usage
```hcl
data "valtix_policy_rule_set" "ingress_policy_rule_set" {
  name = "ingress_rule_set"
}

resource "valtix_gateway" aws-gw1 {
  # other arguments hidden for brevity
  policy_rule_set_id = data.valtix_policy_rule_set.ingress_policy_rule_set.rule_set_id
}
```

## Argument Reference
* `name` - (Required) Name of the Rule Set

## Attributes Reference
* `rule_set_id` - Set to the RuleSetID of the found resource