# Resource: valtix_profile_urlfilter

Create FQDN filtering profile

## Example Usage

```hcl
resource "valtix_profile_fqdn" "url1" {
  name        = "fqdn2"
  description = "fqdn filter"
  fqdn_filter_list {
    fqdn_list            = ["www.website1.com", "www.website2.com"]
    policy               = "ALLOW_LOG"
    decryption_exception = false
  }
  fqdn_filter_list {
    fqdn_list            = ["www.website3.com", "www.website4.com"]
    policy               = "DENY_NOLOG"
    decryption_exception = true
  }
  default_fqdn_filter {
    policy               = "DENY_NOLOG"
    decryption_exception = false
  }
}
```

## Argument Reference

* `name` - (Required) Name of the profile
* `description` - (Optional) Description of the profile
* `fqdn_filter_list` - (Required) List of fqdn_filter resources. Structure [defined below](#fqdn-filter)
* `default_fqdn_filter` - (Optional) Default FQDN filter behavious. Structure [defined below](#fqdn-filter)


## FQDN Filter

* `fqdn_list` - (Required) List of strings or regular expressions or predefined Categories
* `policy` - (Required) Action to take on the matching url (and method) "ALLOW_LOG", "ALLOW" (does not log the flow), "DENY_NOLOG" (does not log the flow), "DENY" (log the flow).
* `decryption_exception` - (Optional) true/false. In the proxy mode disable decryption and inspection of packets. Defaults to true.

## Attribute Reference

* `profile_id` - Id of the profile that can be referenced in other resources (e.g. valtix_policy_rules)
