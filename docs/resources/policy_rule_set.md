# Resource: valtix_policy_rule_set
Resource for creating and managing a Policy Rule Set.  A Policy Rule Set is a list of Policy Rules that define the segmentation and security policy for protecting traffic.  The Policy Rule Set can be applied to multiple Gateways across multiple clouds to accommodate a dynamic multi-cloud policy.

## Example Usage
```hcl
resource "valtix_policy_rule_set" "ingress_policy_rule_set" {
  name = "ingress_rule_set"
}
```

## Argument Reference
* `name` - (Required) Name of the Rule Set
* `description` - (Optional) Description of the Rule Set

## Attribute Reference
* `rule_set_id` - ID of the Policy Rule Set that can be referenced in other resources (e.g., *valtix_policy_rules*)