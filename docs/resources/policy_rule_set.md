~> **Note on valtix_policy_rule_set and valtix_policy_rules**
Terraform currently provides both a standalone *valtix_policy_rules* resource and a *valtix_policy_rule_set* resource with rules defined in-line. At this time you cannot use a *valtix_policy_rule_set* with in-line rules in conjunction with any *valtix_policy_rules* resource. Doing so will cause a conflict of rule settings and will overwrite rules. It is recommended to use *valtix_policy_rules* to define all the firewall policies.
Use *valtix_policy_rule_set* resource to just define the (empty) policy rule set only

# Resource: valtix_policy_rule_set
Resource for creating and managing a Policy Ruleset.  A Policy Ruleset is a list of Policy Rules that define the segmentation and security policy for protecting traffic.  The Policy Ruleset can be applied to multiple Gateways to accommodate a dynamic multi-cloud policy.

## Example Usage

```hcl
resource "valtix_policy_rule_set" "ingress_policy_rule_set" {
  name = "ingress_rule_set"
}
```

## Argument Reference
* `name` - (Required) Name of the rule set
* `description` - (Optional) Description of the rule set
* `rule` - (Deprecated) This block can be repeated multiple times. Look below for the [definition/structure](#rule) of the rule

## Rule
* `name` - (Required) Rule name.
* `description` - (Optional) Rule detailed description.
* `state` - (Required) `ENABLED` or `DISABLED`. Set the rule's state to enabled or disabled.
* `type` - (Required) `ReverseProxy`, `ForwardProxy`, `Forwarding`
* `source` - (Optional) address_id of the valtix_address_object. Defaults to "any". *e.g. valtix_address_object.src1.address_id*
* `destination` - (Optional) address_id of the valtix_address_object. Defaults to "any". *e.g. valtix_address_object.dst1.address_id*
* `service` - (Required) Service id of the valtix_service_object. The service object's service_type must match the rule type
* `action` - (Required) `ALLOW_LOG` (log the event), `ALLOW` (do not log the event), `DENY` (log the event), `DENY_NOLOG` (do not log the event).  Events are viewed in the Valtix UI (Investigate -> Flow Analytics).
* `network_intrusion_profile` - (Optional) profile_id of the valtix_profile_network_intrusion. *e.g. valtix_profile_network_intrusion.ips1.profile_id*
* `dlp_profile` - (Optional) profile_id of the valtix_profile_dlp. *e.g. valtix_profile_dlp.dlp1.profile_id*
* `web_protection_profile` - (Optional) profile_id of the valtix_profile_web_protection. *e.g. valtix_profile_web_protection.waf1.profile_id*
* `fqdn_filter_profile` - (Optional) profile_id of the valtix_profile_fqdn_filter. *e.g. valtix_profile_fqdn_filter.waf1.profile_id*
* `anti_virus_profile` - (Optional) profile_id of the valtix_profile_anti_virus. *e.g. valtix_profile_anti_virus.waf1.profile_id*
* `url_filter` - (Optional) profile_id of the valtix_profile_url_filter. *e.g. valtix_profile_url_filter.url1.profile_id*
* `malicious_src_profile` - (Optional) profile_id of the valtix_profile_malicious_src_profile. *e.g. valtix_profile_malicious_src_profile.src1.profile_id*
* `packet_capture_enabled` - (Optional) true/false. Capture pcap when traffic matches the rule.

## Attribute Reference
* `rule_set_id` - ID of the Ruleset that can be referenced in other resources (e.g., *valtix_policy_rules*)
