# Resource: valtix_profile_application_threat
Resource for creaeting and managing a Web Application Firewall (WAF) Profile

## Example Usage

### With auto updating rulesets and other defaults
```hcl
resource "valtix_profile_application_threat" "waf_auto" {
  name           = "waf_auto"
  description    = "waf basic rules"
  paranoia_level = 1
  request_inspection_profile = [
    "Request - Protocol Enforcement"
  ]
  response_inspection_profile = [
    "Response - General"
  ]
}
```

### With manually specified rulesets and other optional/advanced attributes
```hcl
resource "valtix_profile_application_threat" "waf_manual" {
  name                      = "waf_manual"
  description               = "waf profile 1"
  crs_ruleset_version       = "3.0.2-01272021"
  trustwave_ruleset_version = "3.0.2-01272021"
  paranoia_level            = 3
  action                    = "BLOCK"
  request_inspection_profile = [
    "Request - Protocol Enforcement",
    "Request - Protocol Attack"
  ]
  response_inspection_profile = [
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
* `name` - (Required) Name of the profile
* `description` - (Optional) Description of the profile
* `auto_update_crs` - (Optional) Auto Update the CRS Ruleset version. Valid values are *true* or *false*.  Default (if unspecified) is *true*.
* `delay_by_days_crs` - (Optional) Number of days to delay updating the CRS Application Threats Ruleset version after it has been published by Valtix. Valid values are integers from 0 to 30.  A value of *0* means immediate update (0 days).  The default value is *7* (7 days). Valtix publishes new Rulesets as soon as updates are available from the Vendor and complete testing by Valtix.
* `crs_ruleset_version` - (Optional) CRS (Core Rule Set) version. Find the values from the UI. The rulesets are published everyday. Unless you want to use a specific version, Valtix recommends to use auto_update as described above

  If this argument is specified, Auto Update of CRS ruleset is disabled and the profile will only use this version for CRS ruleset.
* `auto_update_trustwave` - (Optional) Auto update the Trustwave Rule Set version daily with a delay specified by `delay_by_days_trustwave` parameter. The valid values are true/false and it is true by default..
* `delay_by_days_trustwave` - (Optional) How many days before we use a Trustwave Rule Set version after it has been published by Valtix. The default for this argument is 7 days, meaning that after the Jan 1st ruleset is published by Valtix, it is applied to the profile, and hence all the gateways using the profile, on Jan 8th. Valtix publishes new rulesets every day except when our internal testing fails.
* `trustwave_ruleset_version` - (Optional) Trustwave Rule Set version. Check Valtix Dashboard (UI) for a valid list of versions or contact Valtix support
  If this argument is specified, Auto Update of Trustwave Rule Set is disabled and the profile will only use this version for Trustwave Rule Set.
* `paranoia_level` - (Required) An integer between 1 and 4. Higher number leads to more false positives but also helps in detecting more attacks. Recommended value is 1
* `request_anamoly` - (Optional) Request anomaly score used in the mod_security anomaly scoring system. Default value is 3
* `response_anamoly` - (Optional) Response anomaly score used in the mod_security anomaly scoring system. Default value is 3
* `pcap` - (Optional) true/false. Capture pcap when attacks are detected
* `api_logging` - (Optional) true/false. Log API requests
* `request_inspection_profile` - (Optional) List of Request attacks from the Core Rule Set
* `response_inspection_profile` - (Optional) List of Response attacks from the Core Rule Set
* `advanced_application_profile` - (Optional) Advanced attacks from the trustwave rule set
* `action` - (Optional) Default "BLOCK" (Deny Log). Can be "DETECT" (Allow Log).
* `rule_event_filter` - (Optional) Rate Limit / Sample a set of rules. Structure is [defined below](#rule-event-filter)
* `event_suppressor` - (Optional) Suppress a given set of rule ids for traffic from certain sources. Structure is [defined below](#event-suppressor)
* `profile_event_filter` - (Optional) Similar to the rule_event_filter but applies to the whole profile instead of specific rule(s). Structure is [defined below](#profile-event-filter)

## Rule Event Filter
* `rule_ids` - (Optional) List of rule ids to filter
* `type` - (Optional) "RATE" or "SAMPLE". When "RATE" is selected, number_of_events and time must be provided. action is applied once the provided rule_ids match the given count in the given time.

  If the type is "SAMPLE", the action is applied once the count of the events matches
* `number_of_events` - (Optional) Number of times the rule id attack must match before the action is applied
* `time` - (Optional) Used when the type is "RATE", the number_of_events must match in the given time (in seconds)

## Event Suppressor
* `source_ips` - (Optional) List of source ips or CIDRs
* `rule_ids` - (Optional) List of rule ids to filter

## Profile Event Filter
* `rule_ids` - (Optional) List of rule ids to filter
* `type` - (Optional) "RATE" or "SAMPLE". When "RATE" is selected, number_of_events and time must be provided. action is applied once the provided rule_ids match the given count in the given time.

  If the type is "SAMPLE", the action is applied once the count of the events matches

## Attribute Reference
* `profile_id` - ID of the Profile that can be referenced in other resources (e.g., *valtix_policy_rules*)
