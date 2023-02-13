# Resource: valtix_profile_malicious_ip
Resource for creating and managing a Malicious IP (MIP) Profile

## Example Usage

### With auto updating Ruleset with delay by 0 days and XFF match
```hcl
resource "valtix_profile_malicious_ip" "mips_auto" {
  name          = "mips_auto"
  description   = "description"
  auto_update   = true
  delay_by_days = 0
  match_xff     = true
}
```

### With manually specified Ruleset and other parameters
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
* `ip_reputation_enabled` - (Optional) Defines whether the Malicious IP Profile is enabled or disabled.  Valid values are `true` (enabled) or `false` (disabled). If not specified, the default value is `false`.
* `auto_update` - (Optional) Auto update the Trustwave Malicious IP Ruleset version. Valid values are `true` or `false`.  If not specified, the default value is `true`.
* `delay_by_days` - (Optional) Number of days to delay updating the Trustwave Malicious IP Ruleset version after it has been published by Valtix. Valid values are integers from 0 to 30.  A value of `0` means immediate update (0 days).  The default value is `0` (0 days). Valtix publishes new Rulesets as soon as updates are available from the Vendor and complete testing by Valtix.
* `vendor_rule_set_name` - (Optional) Trustwave IP Reputation Ruleset version. Applicable values can be found from within the UI. The Rulesets are published everyday. Unless you want to use a specific version, Valtix recommends to use auto_update as described above.  If this argument is specified, Auto Update of Trustwave IP Reputation Ruleset is disabled and the Profile will only use the specified version for Trustwave IP Reputation Ruleset.
* `match_xff` - (Optional) `true` or `false`.  Defines whether the IP information available in the X-Forwarded-For HTTP request header should be evaluated. When not specified, the default value is `false`. (Even though this argument is optional, it is recommended to specify a value explicitly, as the default value may change in the future).

## Attribute Reference
* `id` - ID of the Malicious IP Profile resource that can be referenced in other resources (e.g., *valtix_policy_rules*)
* `profile_id` - (Deprecated) Same as the `id` attribute

## Import
[*Public Preview*] Malicious IP (MIP) Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_malicious_ip.mips_auto 10
```