# Resource: valtix_policy_rule_set
Resource for creating and managing a Policy Rule Set.  A Policy Rule Set is a list of Policy Rules that define the segmentation and security policy for protecting traffic.  The Policy Rule Set can be applied to multiple Gateways across multiple clouds to accommodate a dynamic multi-cloud policy.

~> **Note on valtix_policy_rules and valtix_policy_rule_set**
Terraform currently provides both a standalone valtix_policy_rules resource and a valtix_policy_rule_set resource with Rules defined in-line. At this time you cannot use a valtix_policy_rule_set with in-line Rules in conjunction with any valtix_policy_rules resource. Doing so will cause a conflict of Rule settings and will overwrite the Rules. It is recommended to use the Rules resource (valtix_policy_rules) to define all Rules affiliated with each Policy Rule Set.

## Example Usage
```hcl
resource "valtix_policy_rule_set" "ingress_policy_rule_set" {
  name = "ingress_rule_set"
}
```

## Argument Reference
* `name` - (Required) Name of the Rule Set
* `description` - (Optional) Description of the Rule Set
* `rule` - A list of Policy Rules.  This block can be repeated multiple times. Structure is [documented below](#rule).

### Rule
* `name` - (Required) Name of the Rule
* `description` - (Optional) Description of the Rule
* `state` - (Required) `ENABLED` or `DISABLED`. Specifies whether the rule is enabled or disabled.
* `type` - (Required) `ReverseProxy`, `ForwardProxy`, and `Forwarding`.  Specifies how traffic will be processed by the Rule.
* `source` - (Optional) Unique ID (address_id) of the Address Object (valtix_address_object). If not specified, the default value is the ID of the `any` Address Object.  To obtain the ID of the pre-defined Address Objects (`any`, `internet`, `any-private-rfc1918`), use the Address Object Data Source (see example usage above). (e.g., *valtix_address_object.any_ag.address_id*).
* `destination` - (Optional) Unique ID (address_id) of the Address Object (valtix_address_object). If not specified, the default value is the ID of the `any` Address Object.  To obtain the ID of the pre-defined Address Objects (`any`, `internet`, `any-private-rfc1918`), use the Address Object Data Source (see example usage above). (e.g., *valtix_address_object.internet_ag.address_id*).
* `service` - (Required) Unique ID (service_id) of the Service Object (valtix_service_object). The Service Object `service_type` argument must match the Rule `type` argument. (e.g., *valtix_service_object.tcp_all_ports.service_id*).
* `action` - (Required) `ALLOW_LOG` (log the event), `ALLOW` (do not log the event), `DENY` (log the event), `DENY_NOLOG` (do not log the event).  Specifies whether to allow or deny the traffic, and whether the event should be logged or not.  Events are viewed in the Valtix UI (Investigate -> Flow Analytics).
* `network_intrusion_profile` - (Optional) Unique ID (profile_id) of the IDS/IPS Profile (valtix_profile_network_intrusion). (e.g., *valtix_profile_network_intrusion.ips1.profile_id*).
* `dlp_profile` - (Optional) Unique ID (profile_id) of the DLP Profile (valtix_profile_dlp). (e.g., *valtix_profile_dlp.dlp1.profile_id*).
* `web_protection_profile` - (Optional) Unique ID (profile_id) of the WAF Profile (valtix_profile_web_protection). (e.g., *valtix_profile_web_protection.waf1.profile_id*).
* `fqdn_filter_profile` - (Optional) Unique ID (profile_id) of the FQDN Filtering Profile (valtix_profile_fqdn_filter). (e.g., *valtix_profile_fqdn_filter.waf1.profile_id*).
* `anti_virus_profile` - (Optional) Unique ID (profile_id) of the AV Profile (valtix_profile_anti_virus). (e.g., *valtix_profile_anti_virus.waf1.profile_id*).
* `url_filter` - (Optional) Unique ID (profile_id) of the URL Filtering Profile (valtix_profile_url_filter). (e.g., *valtix_profile_url_filter.url1.profile_id*).
* `malicious_src_profile` - (Optional) Unique ID (profile_id) of the Malicious IP Profile (valtix_profile_malicious_src_profile). (e.g., *valtix_profile_malicious_src_profile.src1.profile_id*).
* `packet_capture_enabled` - (Optional) `true` or `false`. Specifies the Packet Capture Profile to enable packet capture for each session that is processed by the Rule.  If not specified, the default value is `false`.

## Attribute Reference
* `rule_set_id` - ID of the Rule Set that can be referenced in other resources (e.g., *valtix_policy_rules*)