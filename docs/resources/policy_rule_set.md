# Resource: valtix_policy_rule_set

Resource for creating and managing a Policy Rule Set.  A Policy Rule Set is a list of Policy Rules that define the segmentation and security policy for protecting traffic.  The Policy Rule Set can be applied to multiple Gateways across multiple clouds to accommodate a dynamic multi-cloud policy.

## Example Usage

### Standalone Policy Rule Set
```hcl
resource "valtix_policy_rule_set" "ingress_policy_rule_set" {
  name = "ingress_rule_set"
}
```

### Group Policy Rule Set
```hcl
resource "valtix_policy_rule_set" "ingress_policy_rule_set" {
  name               = "ingress_rule_set"
  type               = "GROUP"
  child_rule_set_ids = [1,2,4,3]
}
```

## Argument Reference
* `name` - (Required) Name of the Policy Rule Set
* `description` - (Optional) Description of the Policy Rule Set
* `type` - (Optional) Specifies whether the Policy Rule Set is a Group Policy Rule Set.  The only applicable value is `GROUP`.  If not specified, the Policy Rule Set operates as a Standalone (non-Group) Policy Rule Set.
* `child_rule_set_ids` - (Required) Specifies the set of one or more Standalone (non-Group) Policy Rule Set IDs as an ordered list.  This argument is required ONLY when `type = GROUP`.  When `type` is not specified, the Policy Rule Set is Standalone (non-Group) and this argument does not apply.

## Attribute Reference
* `rule_set_id` - ID of the Policy Rule Set that can be referenced in other resources (e.g., *valtix_policy_rules*, *valtix_policy_rule_set*)