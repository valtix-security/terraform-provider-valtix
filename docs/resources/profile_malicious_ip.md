# Resource: valtix_profile_malicious_ip
Create Malicious IPs Profile

## Example Usage

### With auto updating ruleset and other defaults
```hcl
resource "valtix_profile_malicious_ip" "mips_auto" {
  name        = "mips_auto"
  description = "description"
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
* `auto_update` - (Optional) Auto Update the Trustwave Malicious IP Ruleset version. Valid values are *true* or *false*.  Default (if unspecified) is *true*.
* `delay_by_days` - (Optional) Number of days to delay updating the Trustwave Malicious IP Ruleset version after it has been published by Valtix. Valid values are integers from 0 to 30.  A value of *0* means immediate update (0 days).  The default value is *7* (7 days). Valtix publishes new Rulesets as soon as updates are available from the Vendor and complete testing by Valtix.
* `vendor_rule_set_name` - (Optional) Vendor rule set name/version. Find the values from the UI. The rulesets are published everyday. Unless you want to use a specific version, Valtix recommends to use auto_update as described above

  If this argument is specified, Auto Update of Trustwave IP Reputation Ruleset is disabled and the profile will only use this version for Trustwave IP Reputation Ruleset.

## Attribute Reference
* `profile_id` - ID of the Profile that can be referenced in other resources (e.g., *valtix_policy_rules*)
