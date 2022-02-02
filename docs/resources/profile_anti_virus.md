# Resource: valtix_profile_anti_virus
Create Antivirus Profile

## Example Usage

### With auto updating ruleset and other defaults
```hcl
resource "valtix_profile_anti_virus" "av_auto" {
  name        = "av_auto"
  description = "description"
  action      = "DROP"
}
```

### With manually specified ruleset version
```hcl
resource "valtix_profile_anti_virus" "av_manual" {
  name                  = "av_manual"
  description           = "description"
  action                = "DROP"
  talos_ruleset_version = "0.103.0-01272021"
}
```

## Argument Reference
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `auto_update` - (Optional) Auto update the Talos ClamAV Ruleset version with a delay specified by `delay_by_days` argument. Applicable values are `true` or `false`.  If not specified, the default value is `true`.
* `delay_by_days` - (Optional) Number of days to delay updating the Talos ClamAV Ruleset version after it has been published by Valtix. Applicable values are integers from `0` to `30`.  A value of `0` means immediate update (0 days). The default value is `7` (7 days). New Rulesets as published as soon as updates are available from the Vendor and validation testing is completed by Valtix.
* `talos_ruleset_version` - (Optional) Talos ClamAV Ruleset version. Applicable values can be found from within the UI. The Rulesets are published frequently. Unless a specific version is desired, Valtix recommends using Auto Update as described above. If this argument is specified, Auto Update of Talos ClamAV Ruleset is disabled and the Profile will use the specified Talos ClamAV Ruleset version.
* `action` - (Optional) Action to take when a Web Application (WAF) Application Threat is detected. Applicable values: `Allow Log` (allow and log the event), `Deny Log` (deny and log the event).  If not specified, then the action assumed is `Deny Log`.
* `action` - (Optional) Action to take when a Antivirus Network Threat is detected. Applicable values: `Allow Log` (allow and log the event), `Deny Log` (deny and log the event). Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event). If not specified, then the action assumed is `Allow Log`.

## Attribute Reference
* `profile_id` - ID of the Profile that can be referenced in other resources (e.g., *valtix_policy_rules*)