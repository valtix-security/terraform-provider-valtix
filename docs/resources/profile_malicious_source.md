# Resource: valtix_profile_malicious_source

Create Malicious Sources Profile

## Example Usage

```hcl
resource "valtix_profile_malicious_source" msrc_auto {
  name                  = "msrc_auto"
  description           = "description"
  ip_reputation_enabled = true
}
```

```hcl
resource "valtix_profile_malicious_source" msrc_manual {
  name                  = "msrc_manual"
  description           = "description"
  ip_reputation_enabled = true
  vendor_rule_set_name  = "1.0.0-01272021"
}
```

## Argument Reference

* `name` - (Required) Name of the Malicious Sources profile
* `description` - (Optional) Description of the Malicious Sources profile
* `ip_reputation_enabled` - (Optional) true/false
* `auto_update` - (Optional) Auto update the Trustwave IP Reputation Ruleset version daily with a delay specified by `delay_by_days` parameter. The valid values are true/false and it is true by default.
* `delay_by_days` - (Optional) How many days before we use a Trustwave IP Reputation Ruleset version after it has been published by Valtix. The default for this argument is 7 days, meaning that after the Jan 1st ruleset is published by Valtix, it is applied to the profile, and hence all the gateways using the profile, on Jan 8th. Valtix publishes new rulesets every day except when our internal testing fails.
* `vendor_rule_set_name` - (Optional) Vendor rule set name/version. Valid values are (as of this publication of this document):
    * **1.0.0-01272021**
    * **1.0.0-01262021**
    * **1.0.0-01252021**
    * **1.0.0-01242021**
    * **1.0.0-01232021**
    * **1.0.0-01222021**

  If this argument is specified, Auto Update of Trustwave IP Reputation Ruleset is disabled and the profile will only use this version for Trustwave IP Reputation Ruleset.
