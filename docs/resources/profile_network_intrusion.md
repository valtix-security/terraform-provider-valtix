# Resource: valtix_profile_network_intrusion
Create Network Intrusion (IPS) Profile

## Example Usage

### With auto updating ruleset and other defaults
```hcl
resource "valtix_profile_network_intrusion" "ips_auto" {
  name          = "ips_auto"
  description   = "predefined rules tagged as 'connectivity'"
  policy        = "CONNECTIVITY"
  policy_action = "ALERT"
}
```

### With manually specified ruleset version
```hcl
resource "valtix_profile_network_intrusion" "ips_manual" {
  name                  = "ips_manual"
  description           = "predefined rules tagged as 'connectivity'"
  policy                = "CONNECTIVITY"
  policy_action         = "ALERT"
  talos_ruleset_version = "2.9.11.1-01272021"
}
```

### Advanced Example with all the attributes
```hcl
resource "valtix_profile_network_intrusion" "ips_manual" {
  name                  = "ips_manual"
  description           = "predefined rules tagged as 'connectivity'"
  policy                = "CONNECTIVITY"
  action                = "ALERT"
  policy_action         = "ALERT"
  talos_ruleset_version = "2.9.11-07092021"
  categories {
    name   = "app-detect"
    action = "ALERT"
  }
  categories {
    name   = "browser-chrome"
    action = "ALERT"
  }
  classes {
    name   = "attempted-admin"
    action = "ALERT"
  }
  classes {
    name   = "attempted-dos"
    action = "ALERT"
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
* `auto_update` - (Optional) Auto Update the Talos Network Intrusion Ruleset version. Valid values are *true* or *false*.  Default (if unspecified) is *true*.
* `delay_by_days` - (Optional) Number of days to delay updating the Talos Network Intrusion Ruleset version after it has been published by Valtix. Valid values are integers from 0 to 30.  A value of *0* means immediate update (0 days).  The default value is *7* (7 days). Valtix publishes new Rulesets as soon as updates are available from the Vendor and complete testing by Valtix.
* `talos_ruleset_version` - (Optional) The Talos Network Intrusion Ruleset version. Valid values are available in the Valtix UI (Administration -> Management -> System -> Talos / Network Intrusion). If this argument is specified, Auto Update of Talos Network Intrusion Ruleset is disabled and the Profile will only use this Talos Network Intrusion version. Valtix recommends using Auto Update to ensure the latest Rulesets are used.
* `action` - (Optional) Action to take when a Network Intrusion is detected. This action can be overwritten for each Network Intrusion attack type (default value is specified for each Rule in the Talos Network Intrusion Ruleset). If not specified, then the action assumed is the action defined in the Policy Ruleset Rule (Rule Default). Valid values:
    * **ALERT** (Allow Log - log the event)
    * **DROP** (Deny Log - log the event)
    * **SILENTDROP** (Deny No Log - do not log the event)
* `policy` - (Required) Pre-defined Talos Network Intrusion Ruleset to use. Valid values:
    * **CONNECTIVITY**
    * **BALANCED**,
    * **SECURITY**
    * **MAX_DETECT**
* `policy_action` - (Optional) Action to take when a Network Intrusion is detected for a pre-defined Policy. Valid values are the same values as *action*. If not specified, then the action assumed is the action defined in the *action* argument.
* `categories` - (Optional) Pre-defined Categories. Structure [defined below](#categories). This block can be repeated multiple times.
* `classes` - (Optional) Predefined classes. Structure [defined below](#classes). This block can be repeated multiple times.
* `pcap` - (Optional) Capture packets (PCAP) when traffic matches the Network Intrusion rules.  Valid values are *true* or *false*.
* `rule_event_filter` - (Optional) Rate Limit / Sample a set of rules. Structure[defined below](#rule-event-filter). This block can be repeated multiple times.
* `event_suppressor` - (Optional) Suppress a given set of Rule IDs for traffic from certain sources. Structure [defined below](#event-suppressor). This block can be repeated multiple times.
* `profile_event_filter` - (Optional) Similar to the *rule_event_filter* but applies to the entire Profile instead of specific Rule(s).  Structure [defined below](#profile-event-filter)

## Categories
* `name` - (Required) Name of a Talos Network Intrusion attack category
* `action` - (Optional) Values same as action attribute. If this is not specified, then the value defined in 'action' attribute is used

## Classes
* `name` - (Required) Name of IPS attacks classes
* `action` - (Optional) Values same as action attribute. If this is not specified, then the value defined in 'action' attribute is used

## Rule Event Filter
* `rule_ids` - (Optional) List of Rule IDss to filter
* `type` - (Optional) "RATE" or "SAMPLE". When "RATE" is selected, number_of_events and time must be provided. action is applied once the provided rule_ids match the given count in the given time.

  If the type is "SAMPLE", the action is applied once the count of the events matches

* `number_of_events` - (Optional) Number of times the rule id attack must match before the action is applied
* `time` - (Optional) Used when the type is "RATE", the number_of_events must match in the given time (in seconds)

## Event Suppressor
* `source_ips` - (Optional) List of source IPs or CIDRs
* `rule_ids` - (Optional) List of Rule IDs to filter

## Profile Event Filter
* `rule_ids` - (Optional) List of Rule IDs to filter
* `type` - (Optional) "RATE" or "SAMPLE". When "RATE" is selected, number_of_events and time must be provided. action is applied once the provided rule_ids match the given count in the given time.

  If the type is "SAMPLE", the action is applied once the count of the events matches

## Attribute Reference
* `profile_id` - ID of the Profile that can be referenced in other resources (e.g., *valtix_policy_rules*)