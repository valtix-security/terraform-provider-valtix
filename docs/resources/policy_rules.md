# Resource: valtix_policy_rules
Resource for creating and managing the segmentation and security Policy Rules associated with a Policy Rule Set.

## Example Usage

### Ingress Policy Rules
```hcl
resource "valtix_policy_rule_set" "ingress_policy" {
  name = "ingress-policy-ruleset"
}

resource "valtix_policy_rules" "ingress_policy_rules" {
  rule_set_id = valtix_policy_rule_set.ingress_policy.rule_set_id
  rule {
    name        = "rule1"
    description = "listen port 80 to target port 80 on app1"
    type        = "ReverseProxy"
    action      = "Allow Log"
    service     = valtix_service_object.app1-svc-http.service_id
    state       = "ENABLED"
  }
  rule {
    name        = "rule2"
    description = "listen port 443 to target port 443 on app1"
    type        = "ReverseProxy"
    action      = "Allow Log"
    service     = valtix_service_object.app1-svc-https.service_id
    state       = "DISABLED"
  }
}
```

### Egress/East-West Policy Rules
```hcl
resource "valtix_policy_rule_set" "egress_ew_policy" {
  name = "egress-ew-policy-ruleset"
}

data "valtix_address_object" "any_ag" {
  name = "any"
}

data "valtix_address_object" "any_private_rfc1918_ag" {
  name = "any-private-rfc1918"
}

data "valtix_address_object" "internet_ag" {
  name = "internet"
}

resource "valtix_policy_rules" "egress-ew-policy-rules" {
	rule_set_id = valtix_policy_rule_set.egress_ew_policy.rule_set_id
	rule {
		name        = "private-to-private"
		action      = "Allow Log"
		state       = "ENABLED"
		service     = valtix_service_object.tcp_all_ports.service_id
		source      = data.valtix_address_object.any_private_rfc1918_ag.address_id
		destination = data.valtix_address_object.any_private_rfc1918_ag.address_id
		type        = "Forwarding"
	}
	rule {
		name        = "private-to-internet"
		action      = "Allow Log"
		state       = "ENABLED"
		service     = valtix_service_object.tcp_all_ports.service_id
		source      = data.valtix_address_object.any_private_rfc1918_ag.address_id
		destination = data.valtix_address_object.internet_ag.address_id
		type        = "Forwarding"
	}
	rule {
		name        = "any-to-any"
		action      = "Deny Log"
		state       = "ENABLED"
		service     = valtix_service_object.tcp_all_ports.service_id
		source      = data.valtix_address_object.any_ag.address_id
		destination = data.valtix_address_object.any_ag.address_id
		type        = "Forwarding"
	}
}
```

## Argument Reference
* `rule_set_id` - (Required) Unique ID (rule_set_id) of the Policy Rule Set. (e.g., *valtix_policy_rule_set.egress_ew_policy.rule_set_id*).
* `rule` - A list of Policy Rules.  This block can be repeated multiple times. Structure is [documented below](#rule).

### Rule
* `name` - (Required) Name of the Rule
* `description` - (Optional) Description of the Rule
* `state` - (Required) `ENABLED` or `DISABLED`. Specifies whether the rule is enabled or disabled.
* `type` - (Required) `ReverseProxy`, `ForwardProxy`, and `Forwarding`.  Specifies how traffic will be processed by the Rule.
* `source` - (Optional) Unique ID (address_id) of the Address Object (valtix_address_object). If not specified, the default value is the ID of the `any` Address Object.  To obtain the ID of the pre-defined Address Objects (`any`, `internet`, `any-private-rfc1918`), use the Address Object Data Source (see example usage above). (e.g., *valtix_address_object.any_ag.address_id*).
* `destination` - (Optional) Unique ID (address_id) of the Address Object (valtix_address_object). If not specified, the default value is the ID of the `any` Address Object.  To obtain the ID of the pre-defined Address Objects (`any`, `internet`, `any-private-rfc1918`), use the Address Object Data Source (see example usage above). (e.g., *valtix_address_object.internet_ag.address_id*).
* `service` - (Required) Unique ID (service_id) of the Service Object (valtix_service_object). The Service Object `service_type` argument must match the Rule `type` argument. (e.g., *valtix_service_object.tcp_all_ports.service_id*).
* `action` - (Required) Action to take when traffic matches the Rule. Applicable values: `Allow Log` (log the event), `Allow No Log` (do not log the event), `Deny Log` (log the event), `Deny No Log` (do not log the event). Action names were changed in release 22.06 of the Valtix Terraform Provider. The previous action names (`ALLOW` `ALLOW_LOG`, `DENY`, `DENY_LOG`) are still valid, but Terraform will interpret the previous values as configuration changes until the new values are used.
* `network_intrusion_profile` - (Optional) Unique ID (profile_id) of the IDS/IPS Profile (valtix_profile_network_intrusion). (e.g., *valtix_profile_network_intrusion.ips1.profile_id*).
* `dlp_profile` - (Optional) Unique ID (profile_id) of the DLP Profile (valtix_profile_dlp). (e.g., *valtix_profile_dlp.dlp1.profile_id*).
* `web_protection_profile` - (Optional) Unique ID (profile_id) of the WAF Profile (valtix_profile_web_protection). (e.g., *valtix_profile_web_protection.waf1.profile_id*).
* `fqdn_filter_profile` - (Optional) Unique ID (profile_id) of the FQDN Filtering Profile (valtix_profile_fqdn_filter). (e.g., *valtix_profile_fqdn_filter.waf1.profile_id*).
* `anti_virus_profile` - (Optional) Unique ID (profile_id) of the AV Profile (valtix_profile_anti_virus). (e.g., *valtix_profile_anti_virus.waf1.profile_id*).
* `url_filter` - (Optional) Unique ID (profile_id) of the URL Filtering Profile (valtix_profile_url_filter). (e.g., *valtix_profile_url_filter.url1.profile_id*).
* `malicious_src_profile` - (Optional) Unique ID (profile_id) of the Malicious IP Profile (valtix_profile_malicious_src_profile). (e.g., *valtix_profile_malicious_src_profile.src1.profile_id*).
* `packet_capture_enabled` - (Optional) `true` or `false`. Specifies the Packet Capture Profile to enable packet capture for each session that is processed by the Rule.  If not specified, the default value is `false`.