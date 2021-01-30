# Resource: valtix_profile_network_intrusion

Create IPS (Network Intrusion) Profile

## Example Usage

### With auto updating ruleset and other defaults

```hcl
resource "valtix_profile_network_intrusion" ips_auto {
  name                  = "ips_auto"
  description           = "predefined rules tagged as 'connectivity'"
  policy                = "CONNECTIVITY"
  policy_action         = "ALERT"
}
```

### With manually specified ruleset version

```hcl
resource "valtix_profile_network_intrusion" ips_manual {
  name                  = "ips_manual"
  description           = "predefined rules tagged as 'connectivity'"
  policy                = "CONNECTIVITY"
  policy_action         = "ALERT"
  talos_ruleset_version = "2.9.11.1-01272021"
}
```

## Argument Reference

* `name` - (Required) Name of the IPS profile
* `description` - (Optional) Description of the IPS profile
* `auto_update` - (Optional) Auto update the Talos IPS Ruleset version daily with a delay specified by `delay_by_days` parameter. The valid values are true/false and it is true by default..
* `delay_by_days` - (Optional) How many days before we use a Talos IPS Ruleset version after it has been published by Valtix. The default for this argument is 7 days, meaning that after the Jan 1st ruleset is published by Valtix, it is applied to the profile, and hence all the gateways using the profile, on Jan 8th. Valtix publishes new rulesets every day except when our internal testing fails.
* `talos_ruleset_version` - (Optional) Talos IPS ruleset version. Valid values (as of this publication of this document):
    * **2.9.11.1-01272021**
    * **2.9.11.1-01262021**
    * **2.9.11.1-01252021**
    * **2.9.11.1-01242021**
    * **2.9.11.1-01232021**
    * **2.9.11.1-01222021**

  If this argument is specified, Auto Update of Talos IPS Ruleset is disabled and the profile will only use this version for Talos IPS Ruleset.

* `action` - (Optional) Default action for all the attacks. Can be overwritten for each attack type. Default value is specified in the talos rule set. If this is not specified then whatever action that's specified in the ruleset is used (called Rule Default). Valid values:
    * **ALERT** (logs in ips events)
    * **DROP** (drop the traffic and log the events)
    * **SILENTDROP** (drop the traffic and no log of events)
* `policy` - (Required) Pre-defined IPS ruleset to use, must be one of:
    * **CONNECTIVITY**
    * **BALANCED**,
    * **SECURITY**
    * **MAX_DETECT**
* `policy_action` - (Optional) Action to apply for the predefined policy. Look at action attribute for valid values. If this is not specified, the action defined in the 'action' attribute is used.
* `categories` - (Optional) List of predefined categories. Structure [defined below](#categories)
* `classes` - (Optional) List of predefined classes. Structure [defined below](#classes)
* `pcap` - (Optional) true/false. Capture pcap when traffic matches the IPS rules
* `rule_event_filter` - (Optional) Rate Limit / Sample a set of rules. Structure is [defined below](#rule-event-filter)
* `event_suppressor` - (Optional) Suppress a given set of rule ids for traffic from certain sources. Structure is [defined below](#event-suppressor)
* `profile_event_filter` - (Optional) Similar to the rule_event_filter but applies to the whole profile instead of specific rule(s).  Structure is [defined below](#profile-event-filter)

## Categories
* `name` - (Required) Name of IPS attacks categories
* `action` - (Optional) Values same as action attribute. If this is not specified, then the value defined in 'action' attribute is used

## Classes

* `name` - (Required) Name of IPS attacks classes
* `action` - (Optional) Values same as action attribute. If this is not specified, then the value defined in 'action' attribute is used

## Rule Event Filter

* `rule_ids` - (Optional) List of rule ids to filter
* `type` - (Optional) "RATE" or "SAMPLE". When "RATE" is selected, number_of_events and time must be provided. action is applied once the provided rule_ids match the given count in the given time.

If the type is "SAMPLE", the action is applied once the count of the events matces

* `number_of_events` - (Optional) Number of times the rule id attack must match before the action is applied
* `time` - (Optional) Used when the type is "RATE", the number_of_events must match in the given time (in seconds)

## Event Suppressor

* `source_ips` - (Optional) List of source ips or CIDRs
* `rule_ids` - (Optional) List of rule ids to filter

## Profile Event Filter

* `rule_ids` - (Optional) List of rule ids to filter
* `type` - (Optional) "RATE" or "SAMPLE". When "RATE" is selected, number_of_events and time must be provided. action is applied once the provided rule_ids match the given count in the given time.

If the type is "SAMPLE", the action is applied once the count of the events matces
