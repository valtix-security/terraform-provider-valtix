# Resource: valtix_profile_application_threat
Resource for creating and managing a Application Threat (WAF) Profile

## Example Usage

### Auto update CRS Ruleset version
```hcl
resource "valtix_profile_application_threat" "waf_auto" {
  name                        = "waf_auto"
  description                 = "WAF CRS rules (auto)"
  auto_update_crs             = true
  delay_by_days_crs           = 0
  paranoia_level              = 1
  request_inspection_profile  = [
    "Request - Protocol Enforcement"
  ]
  response_inspection_profile = [
    "Response - General"
  ]
}
```

### Manually specified CRS Ruleset version
```hcl
resource "valtix_profile_application_threat" "waf_manual" {
  name                        = "waf_manual"
  description                 = "WAF CRS rules (manual)"
  crs_ruleset_version         = "3.0.2-11032022"
  paranoia_level              = 1
  request_inspection_profile  = [
    "Request - Protocol Enforcement"
  ]
  response_inspection_profile = [
    "Response - General"
  ]
}
```

### Advanced CRS and Trustwave Rulesets with other optional/advanced arguments
```hcl
resource "valtix_profile_application_threat" "waf_auto_advanced" {
  name                         = "waf_auto_advanced"
  description                  = "WAF CRS and Trustwave Rules (manual)"
  auto_update_crs              = true
  delay_by_days_crs            = 0
  auto_update_trustwave        = true
  delay_by_days_trustwave      = 0
  paranoia_level               = 1
  action                       = "Deny Log"
  request_inspection_profile   = [
    "Request - Protocol Enforcement",
    "Request - Protocol Attack"
  ]
  response_inspection_profile  = [
    "Response - General",
    "Response - SQL"
  ]
  advanced_application_profile = [
    "ARSC Really Simple Chat",
    "ASP Product Catalog",
    "AXIGEN Mail Server"
  ]
  rule_event_filter {
    rule_ids         = [1, 2, 3, 6]
    number_of_events = 50
    time             = 5
    type             = "RATE"
  }
  rule_event_filter {
    rule_ids         = [4, 5]
    number_of_events = 20
    type             = "SAMPLE"
  }
  event_suppressor {
    source_ips = ["2.2.2.3", "10.2.1.3", "10.10.0.0/10"]
    rule_ids   = [6, 7]
  }
  profile_event_filter {
    number_of_events = 50
    time             = 5
    type             = "RATE"
  }
}
```

## Argument Reference
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `auto_update_crs` - (Optional) Auto Update of the CRS Ruleset version with a delay specified by `delay_by_days_crs` argument. Applicable values are `true` or `false`.  If not specified, the default value is `true`.
* `delay_by_days_crs` - (Optional) Number of days to delay updating the CRS Ruleset version after it has been published by Valtix. Applicable values are integers from `0` to `30`.  A value of `0` means immediate update (0 days). The default value is `0` (immediately). New Rulesets as published as soon as updates are available from the Vendor and validation testing is completed by Valtix.
* `crs_ruleset_version` - (Optional) CRS Ruleset version. Applicable values can be found from within the UI. The Rulesets are published frequently. Unless a specific version is desired, Valtix recommends using Auto Update as described above. If this argument is specified, Auto Update of CRS Ruleset is disabled and the Profile will use the specified CRS Ruleset version.
* `auto_update_trustwave` - (Optional) Auto update the Trustwave Ruleset version with a delay specified by `delay_by_days_trustwave` argument. Applicable values are `true` or `false`.  If not specified, the default value is `true`.
* `delay_by_days_trustwave` - (Optional) Number of days to delay updating the Trustwave Ruleset version after it has been published by Valtix. Applicable values are integers from `0` to `30`.  A value of `0` means immediate update (0 days). The default value is `0` (immediately). New Rulesets as published as soon as updates are available from the Vendor and validation testing is completed by Valtix.
* `trustwave_ruleset_version` - (Optional) Trustwave Ruleset version. Applicable values can be found from within the UI. The Rulesets are published frequently. Unless a specific version is desired, Valtix recommends using Auto Update as described above. If this argument is specified, Auto Update of Trustwave Ruleset is disabled and the Profile will use the specified Trustwave Ruleset version.
* `disable_trustwave_ruleset` - (Optional) Specifies whether the Trustwave Ruleset is enabled or disabled.  Valid values are `true` or `false`.  Default (if not specified) is `false`.  If disabled, the Trustwave Ruleset auto-update will not occur.  At least one Ruleset must be enabled (Trustwave or Custom). Both cannot be disabled at the same time.  The CRS Ruleset cannot be disabled.
* `auto_update_custom` - (Optional) Auto Update of the Custom Ruleset version with a delay specified by `delay_by_days_crs` argument. Applicable values are `true` or `false`.  If not specified, the default value is `false`.
* `delay_by_days_custom` - (Optional) Number of days to delay updating the Custom Ruleset version after it has been imported by the user. Applicable values are integers from `0` to `30`.  A value of `0` means immediate update (0 days). The default value is `0` (immediately).
* `custom_ruleset_version` - (Optional) Custom Ruleset version. Applicable values can be found from within the UI as specified by the user when defining and importing the Custom Rulesets. Unless a specific version is desired, Valtix recommends using Auto Update. If this argument is specified, Auto Update of Custom Ruleset is disabled and the Profile will use the specified Custom Ruleset version.
* `disable_custom_ruleset` - (Optional) Specifies whether the Custom Ruleset is enabled or disabled.  Valid values are `true` or `false`.  Default (if not specified) is `false`.  If disabled, the Custom Ruleset auto-update will not occur.  At least one Ruleset must be enabled (Trustwave or Custom). Both cannot be disabled at the same time.  The CRS Ruleset cannot be disabled.
* `paranoia_level` - (Required) An integer between `1` and `4`. Higher number leads to more false positives, but also helps in detecting more attacks. Recommended value is `1`.
* `request_anamoly` - (Optional) Request anomaly score used in the Mod Security anomaly scoring system. If not specified, the efault value is `3`.
* `response_anamoly` - (Optional) Response anomaly score used in the Mod Security anomaly scoring system. If not specified, the default value is `3`.
* `pcap` - (Optional) Captures a PCAP when attacks are detected.  Applicable values: `true` or `false`. If not specified, the default value is `false`.
* `api_logging` - (Optional) Logs API requests and generates a HAR file.  Applicable values: `true` or `false`. If not specified, the default value is `false`.
* `request_inspection_profile` - (Optional) List of Request attacks detected by the CRS Ruleset
* `response_inspection_profile` - (Optional) List of Response attacks detected by the CRS Ruleset
* `advanced_application_profile` - (Optional) Advanced attacks detected by the Trustwave Ruleset
* `action` - (Optional) Action to take when a Web Application (WAF) Application Threat is detected. Applicable values: `Allow Log` (allow and log the event), `Deny Log` (deny and log the event).  If not specified, then the action assumed is `Deny Log`.
* `rule_event_filter` - (Optional) Rate Limit / Sample a set of rules. Structure is [defined below](#rule-event-filter)
* `event_suppressor` - (Optional) Suppress a given set of rule ids for traffic from certain sources. Structure is [defined below](#event-suppressor)
* `profile_event_filter` - (Optional) Similar to the rule_event_filter but applies to the whole profile instead of specific rule(s). Structure is [defined below](#profile-event-filter)

## Rule Event Filter
* `rule_ids` - (Optional) List of Rule IDs to filter
* `type` - (Optional) `RATE` or `SAMPLE`. If `RATE` is selected, the `number_of_events` and `time` must be specified. The action is taken once the provided Rule IDs match the number of events within specified time period.  If `SAMPLE` is selected, the `number_of_events` must be specified.  The action is taken once the provided Rule IDs match the number of events.
* `number_of_events` - (Optional) Number of times the attack must match a Rule ID before the action is applied
* `time` - (Optional) Used when the `type` is set to `RATE` where the number of times the attack must match a Rule ID within a specified time period (in seconds) before the action is applied.

## Rule Suppressor
* `source_ips` - (Optional) List of source IPs or CIDRs
* `rule_ids` - (Optional) List of Rule IDs to filter

## Profile Event Filter
* `rule_ids` - (Optional) List of Rule IDs to filter
* `type` - (Optional) `RATE` or `SAMPLE`. If `RATE` is selected, the `number_of_events` and `time` must be specified. The action is taken once the provided Rule IDs match the number of events within specified time period.  If `SAMPLE` is selected, the `number_of_events` must be specified.  The action is taken once the provided Rule IDs match the number of events.
* `number_of_events` - (Optional) Number of times the attack must match a Rule ID before the action is applied
* `time` - (Optional) Used when the `type` is set to `RATE` where the number of times the attack must match a Rule ID within a specified time period (in seconds) before the action is applied.

## Attribute Reference
* `id` - ID of the Application Threat (WAF) Profile resource that can be referenced in other resources (e.g., *valtix_policy_rules*)
* `profile_id` - (Deprecated) Same as the `id` attribute

## Import
[*Public Preview*] Application Threat (WAF) Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_application_threat.waf_auto 10
```