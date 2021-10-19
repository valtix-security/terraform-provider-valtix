# Resource: valtix_profile_malicious_ip
Create Malicious IPs Profile

## Example Usage

### With auto updating ruleset and other defaults
```hcl
resource "valtix_profile_malicious_ip" "mips_auto" {
  name                  = "mips_auto"
  description           = "description"
}
```

### With manually specified ruleset and other parameters
```hcl
resource "valtix_profile_malicious_ip" "mips_manual" {
  name                  = "mips_manual"
  description           = "description"
  ip_reputation_enabled = true
  vendor_rule_set_name  = "1.0.0-01272021"
}
```

## Argument Reference
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the  Profile
* `ip_reputation_enabled` - (Optional) true/false
* `auto_update` - (Optional) Auto update the Trustwave IP Reputation Ruleset version with a delay specified by `delay_by_days` parameter. The valid values are true/false and it is true by default.
* `delay_by_days` - (Optional) How many days before we use a Trustwave IP Reputation Ruleset version after it has been published by Valtix. The default for this argument is 7 days, meaning that after the Jan 1st ruleset is published by Valtix, it is applied to the profile, and hence all the gateways using the profile, on Jan 8th. Valtix publishes new rulesets every day except when our internal testing fails.
* `vendor_rule_set_name` - (Optional) Vendor rule set name/version. Find the values from the UI. The rulesets are published everyday. Unless you want to use a specific version, Valtix recommends to use auto_update as described above

  If this argument is specified, Auto Update of Trustwave IP Reputation Ruleset is disabled and the profile will only use this version for Trustwave IP Reputation Ruleset.

## Attribute Reference
* `profile_id` - ID of the Profile that can be referenced in other resources (e.g., *valtix_policy_rules*)
