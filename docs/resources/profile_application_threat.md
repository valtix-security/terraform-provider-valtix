# Resource: valtix_profile_application_threat

Create Web Application Firewall (WAF) profile

## Example Usage

```hcl
resource "valtix_profile_application_threat" waf1 {
  name                      = "waf1"
  description               = "waf basic rules"
  crs_ruleset_version       = "3.0.2-08262020"
  trustwave_ruleset_version = "3.0.2-08262020"
  paranoia_level            = 1
  request_inspection_profile = [
    "Request - Protocol Enforcement"
  ]
  response_inspection_profile = [
    "Response - General"
  ]
}
```

With all the other optional/advanced attributes

```hcl
resource "valtix_profile_application_threat" waf_profile1 {
  name                      = "waf_profile1"
  description               = "waf profile 1"
  crs_ruleset_version       = "3.0.2-08262020"
  trustwave_ruleset_version = "3.0.2-08262020"
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
  /*
    profile_event_filter {
        number_of_events = 50
        type = "SAMPLE"
    }
    */
}
```

## Argument Reference

* `name` - (Required) Name of the profile
* `description` - (Optional) Description of the profile
* `crs_ruleset_version` - (Required) CRS (Core Rule Set) version. Valid values:
    * **3.0.2-08262020**
    * **3.0.2-08102020**
    * **3.0.2-07302020**
    * **3.0.2-07012020**
    * **3.0.2-06222020**
    * **3.0.2-05102020**
* `trustwave_ruleset_version` - (Required) Trustwave Rule Set version. Valid values are:
    * **3.0.2-08262020**
    * **3.0.2-08102020**
    * **3.0.2-07302020**
    * **3.0.2-07012020**
    * **3.0.2-06222020**
    * **3.0.2-05102020**
* `paranoia_level` - (Required) An integer between 1 and 4. Higher number leads to more false positives but also helps in detecting more attacks. Recommended value is 1
* `request_anamoly` - (Optional) Request anomaly score used in the mod_security anomaly scoring system. Default value is 3
* `response_anamoly` - (Optional) Response anomaly score used in the mod_security anomaly scoring system. Default value is 3
* `pcap` - (Optional) true/false. Capture pcap when attacks are detected
* `api_logging` - (Optional) true/false. Log API requests
* `request_inspection_profile` - (Optional) List of Request attacks from the Core Rule Set
* `response_inspection_profile` - (Optional) List of Response attacks from the Core Rule Set
* `advanced_application_profile` - (Optional) Advanced attacks from the trustwave rule set
* `action` - (Optional) Default "BLOCK". Can be "DETECT"
* `rule_event_filter` - (Optional) Rate Limit / Sample a set of rules. Structure is [defined below](#rule-event-filter)
* `event_suppressor` - (Optional) Suppress a given set of rule ids for traffic from certain sources. Structure is [defined below](#event-suppressor)
* `profile_event_filter` - (Optional) Similar to the rule_event_filter but applies to the whole profile instead of specific rule(s). Structure is [defined below](#profile-event-filter)

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