# Resource: valtix_profile_network_intrusion
Resource for creating and managing a Network Intrusion (IPS) Profile

## Example Usage

### Auto update Talos Ruleset version
```hcl
resource "valtix_profile_network_intrusion" "ips_auto" {
  name          = "ips_auto"
  description   = "predefined rules tagged as 'connectivity'"
  policy        = "CONNECTIVITY"
  policy_action = "Deny Log"
  auto_update   = true
  delay_by_days = 0
}
```

### Manually specified Talos Ruleset version
```hcl
resource "valtix_profile_network_intrusion" "ips_manual" {
  name                  = "ips_manual"
  description           = "predefined rules tagged as 'connectivity'"
  policy                = "CONNECTIVITY"
  policy_action         = "Deny Log"
  talos_ruleset_version = "2.9.11-11032022"
}
```

### Advanced Talos Ruleset with other optional/advanced arguments
```hcl
resource "valtix_profile_network_intrusion" "ips_auto_advanced" {
  name          = "ips_auto_advanced"
  description   = "predefined rules tagged as 'connectivity'"
  policy        = "CONNECTIVITY"
  action        = "Allow Log"
  policy_action = "Deny Log"
  auto_update   = true
  delay_by_days = 0
  categories {
    name   = "app-detect"
    action = "ALERT"
  }
  categories {
    name   = "browser-chrome"
    action = "Deny Log"
  }
  classes {
    name   = "attempted-admin"
    action = "Deny No Log"
  }
  classes {
    name   = "attempted-dos"
    action = "Deny No Log"
  }
  rule_event_filter {
    rule_ids         = ["12345", "12346"]
    type             = "SAMPLE"
    number_of_events = "3"
  }
  rule_event_filter {
    rule_ids         = ["7689"]
    type             = "SAMPLE"
    number_of_events = "3"
  }
  event_suppressor {
    source_ips = ["172.16.1.252", "172.22.0.0/16"]
    rule_ids   = ["12345", "12346"]
  }
  event_suppressor {
    source_ips = ["0.0.0.0"]
    rule_ids   = ["7689"]
  }
  profile_event_filter {
    type             = "SAMPLE"
    number_of_events = "3"
  }
}
```

## Argument Reference
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `auto_update` - (Optional) Auto Update the Talos Network Intrusion Ruleset version. Valid values are `true` or `false`.  Default (if unspecified) is `true`.
* `delay_by_days` - (Optional) Number of days to delay applying the Talos Network Intrusion Ruleset version after it has been published by Valtix. Applicable values are integers from `0` to `30`.  A value of `0` means immediate update (0 days). The default value is `0` (immediately). New Rulesets as published as soon as updates are available from the Vendor and validation testing is completed by Valtix.
* `talos_ruleset_version` - (Optional) Talos Network Intrusion Ruleset version. Applicable values can be found from within the UI. The Rulesets are published frequently. Unless a specific version is desired, Valtix recommends using Auto Update. If this argument is specified, Auto Update of Talos Network Intrusion Ruleset is disabled and the Profile will use the specified Talos Network Intrusion Ruleset version.
* `auto_update_custom` - (Optional) Auto Update the Custom Network Intrusion Ruleset version. Valid values are `true` or `false`.  Default (if unspecified) is `false`.
* `delay_by_days_custom` - (Optional) Number of days to delay applying the Custom Network Intrusion Ruleset version after it has been imported by the user. Applicable values are integers from `0` to `30`.  A value of `0` means immediate update (0 days). The default value is `0` (immediately).
* `custom_ruleset_version` - (Optional) Custom Network Intrusion Ruleset version. Applicable values can be found from within the UI as specified by the user when defining and importing the Custom Rulesets. Unless a specific version is desired, Valtix recommends using Auto Update. If this argument is specified, Auto Update of Custom Network Intrusion Ruleset is disabled and the Profile will use the specified Custom Network Intrusion Ruleset version.
* `action` - (Optional) Action to take when a Network Intrusion (IDS/IPS) Network Threat is detected for Rules that fall into the Profile set (the entire set or Rules defined by the configuration of the Profile). Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event). If not specified, then the action assumed is `Rule Default`, the action defined in the Policy Ruleset Rule. This action can be overridden for each Policy, [Category](#categories) and [Class](#classes) of Rules.
* `policy` - (Required) Pre-defined Talos Network Intrusion Ruleset to use. Applicable values: `CONNECTIVITY`, `BALANCED`, `SECURITY`, `MAX_DETECT`.
* `policy_action` - (Optional) Action to take when a Network Intrusion (IDS/IPS) Network Threat is detected for Rules that fall into the Policy set. Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event). This action is an override to the Profile action. If not specified, then the action assumed is the action defined for the Profile.
* `categories` - (Optional) Pre-defined Categories. Structure [defined below](#categories). This block can be repeated multiple times.
* `classes` - (Optional) Predefined Classes. Structure [defined below](#classes). This block can be repeated multiple times.
* `pcap` - (Optional) Capture packets (PCAP) when traffic matches the Network Intrusion rules.  Valid values are *true* or *false*.
* `rule_event_filter` - (Optional) Rate Limit / Sample a set of rules. Structure [defined below](#rule-event-filter). This block can be repeated multiple times.
* `event_suppressor` - (Optional) Suppress a given set of Rule IDs for traffic from certain sources. Structure [defined below](#event-suppressor). This block can be repeated multiple times.
* `profile_event_filter` - (Optional) Similar to the *rule_event_filter* but applies to the entire Profile instead of specific Rule(s).  Structure [defined below](#profile-event-filter)

## Categories
* `name` - (Required) Name of a Talos Network Intrusion attack Category
* `action` - (Optional) Action to take when a Network Intrusion (IDS/IPS) Network Threat is detected for Rules that fall into the Category set. Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event). This action is an override to the Policy action. If not specified, then the action assumed is the action defined for the Policy.

## Classes
* `name` - (Required) Name of Talos Network Intrusion attack Class
* `action` - (Optional) Action to take when a Network Intrusion (IDS/IPS) Network Threat is detected for Rules that fall into the Class set. Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event). This action is an override to the Categories action. If not specified, then the action assumed is the action defined for the Category.

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
* `id` - ID of the Network Intrusion (IDS/IPS) Profile resource that can be referenced in other resources (e.g., *valtix_policy_rules*)
* `profile_id` - (Deprecated) Same as the `id` attribute