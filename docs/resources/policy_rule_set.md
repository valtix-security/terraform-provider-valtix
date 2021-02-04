# Resource: valtix_policy_rule_set

A policy rule set is a list of firewall rules. The rule set can be applied to
multiple gateways to achieve identical security posture.

## Example Usage

```hcl
resource "valtix_policy_rule_set" ingress_policy_rule_set {
  name = "ingress_rule_set"
  rule {
    name        = "rule1"
    description = "listen port 80 to target port 80 on app1"
    type        = "ReverseProxy"
    action      = "ALLOW_LOG"
    service     = "${valtix_service_object.app1-svc-http.service_id}"
    state       = "ENABLED"
  }
  rule {
    name        = "rule2"
    description = "listen port 443 to target port 443 on app1"
    type        = "ReverseProxy"
    action      = "ALLOW_LOG"
    service     = "${valtix_service_object.app1-svc-https.service_id}"
    state       = "DISABLED"
  }
}
```

## Argument Reference

* `name` - (Required) Name of the rule set
* `description` - (Optional) Description of the rule set
* `rule` - A list of rules, this block can be repeated multiple times. Look below for the [definition/structure](#rule) of the rule

## Rule

* `name` - (Required) Rule name.
* `description` - (Optional) Rule detailed description.
* `state` - (Required) "ENABLED" or "DISABLED". Set the rule's state to enabled or disabled.
* `type` - (Required) **ReverseProxy**, **ForwardProxy**, **Forwarding**
* `source` - (Optional) address_id of the valtix_address_object. Defaults to "any". *e.g. valtix_address_object.src1.address_id*
* `destination` - (Optional) address_id of the valtix_address_object. Defaults to "any". *e.g. valtix_address_object.dst1.address_id*
* `service` - (Required) Service id of the valtix_service_object. The service object's service_type must match the rule type
* `action` - (Required) "ALLOW_LOG", "ALLOW" (does not log the flow), "DENY_NOLOG" (does not log the flow), "DENY" (log the flow)
* `network_intrusion_profile` - (Optional) profile_id of the valtix_profile_network_intrusion. *e.g. valtix_profile_network_intrusion.ips1.profile_id*
* `dlp_profile` - (Optional) profile_id of the valtix_profile_dlp. *e.g. valtix_profile_dlp.dlp1.profile_id*
* `web_protection_profile` - (Optional) profile_id of the valtix_profile_web_protection. *e.g. valtix_profile_web_protection.waf1.profile_id*
* `fqdn_filter_profile` - (Optional) profile_id of the valtix_profile_fqdn_filter. *e.g. valtix_profile_fqdn_filter.waf1.profile_id*
* `anti_virus_profile` - (Optional) profile_id of the valtix_profile_anti_virus. *e.g. valtix_profile_anti_virus.waf1.profile_id*
* `url_filter` - (Optional) profile_id of the valtix_profile_url_filter. *e.g. valtix_profile_url_filter.url1.profile_id*
* `malicious_src_profile` - (Optional) profile_id of the valtix_profile_malicious_src_profile. *e.g. valtix_profile_malicious_src_profile.src1.profile_id*
* `packet_capture_enabled` - (Optional) true/false. Capture pcap when traffic matches the rule.
