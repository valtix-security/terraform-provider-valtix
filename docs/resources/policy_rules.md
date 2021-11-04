~> **Note on valtix_policy_rules and valtix_policy_rule_set**
Terraform currently provides both a standalone valtix_policy_rules resource and a valtix_policy_rule_set resource with rules defined in-line. At this time you cannot use a valtix_policy_rule_set with in-line rules in conjunction with any valtix_policy_rules resource. Doing so will cause a conflict of rule settings and will overwrite rules. It is recommended to use valtix_policy_rules to define all the firewall policies.

# Resource: valtix_policy_rules
Policy Rules is a list of firewall rules that are specified for a Policy Rule Set. 

## Example Usage
```hcl
resource "valtix_policy_rules" "ingress_policy_rules" {
  rule_set_id = valtix_policy_rule_set.ingress_policy_rule_set.rule_set_id
  rule {
    name        = "rule1"
    description = "listen port 80 to target port 80 on app1"
    type        = "ReverseProxy"
    action      = "ALLOW_LOG"
    service     = valtix_service_object.app1-svc-http.service_id
    state       = "ENABLED"
  }
  rule {
    name        = "rule2"
    description = "listen port 443 to target port 443 on app1"
    type        = "ReverseProxy"
    action      = "ALLOW_LOG"
    service     = valtix_service_object.app1-svc-https.service_id
    state       = "DISABLED"
  }
}
```

## Argument Reference
* `rule_set_id` - (Required) The ID of the Policy Rule Set 
* `rule` - A list of rules, this block can be repeated multiple times. Look below for the [definition/structure](#rule) of the rule

## Rule
* `name` - (Required) Rule name.
* `description` - (Optional) Rule detailed description.
* `state` - (Required) "ENABLED" or "DISABLED". Set the rule's state to enabled or disabled.
* `type` - (Required) **ReverseProxy**, **ForwardProxy**, **Forwarding**
* `source` - (Optional) address_id of the valtix_address_object. Defaults to "any". *e.g. valtix_address_object.src1.address_id*
* `destination` - (Optional) address_id of the valtix_address_object. Defaults to "any". *e.g. valtix_address_object.dst1.address_id*
* `service` - (Required) Service id of the valtix_service_object. The service object's service_type must match the rule type
* `action` - (Required) "ALLOW_LOG" (log the event), "ALLOW" (do not log the event), "DENY" (log the event), "DENY_NOLOG" (do not log the event).  Events are viewed in the Valtix UI (Investigate -> Flow Analytics).
* `network_intrusion_profile` - (Optional) profile_id of the valtix_profile_network_intrusion. *e.g. valtix_profile_network_intrusion.ips1.profile_id*
* `dlp_profile` - (Optional) profile_id of the valtix_profile_dlp. *e.g. valtix_profile_dlp.dlp1.profile_id*
* `web_protection_profile` - (Optional) profile_id of the valtix_profile_web_protection. *e.g. valtix_profile_web_protection.waf1.profile_id*
* `fqdn_filter_profile` - (Optional) profile_id of the valtix_profile_fqdn_filter. *e.g. valtix_profile_fqdn_filter.waf1.profile_id*
* `anti_virus_profile` - (Optional) profile_id of the valtix_profile_anti_virus. *e.g. valtix_profile_anti_virus.waf1.profile_id*
* `url_filter` - (Optional) profile_id of the valtix_profile_url_filter. *e.g. valtix_profile_url_filter.url1.profile_id*
* `malicious_src_profile` - (Optional) profile_id of the valtix_profile_malicious_src_profile. *e.g. valtix_profile_malicious_src_profile.src1.profile_id*
* `packet_capture_enabled` - (Optional) true/false. Capture pcap when traffic matches the rule.