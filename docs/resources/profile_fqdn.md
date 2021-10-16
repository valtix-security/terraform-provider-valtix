# Resource: valtix_profile_urlfilter

Create FQDN filtering profile

## Example Usage

```hcl
resource "valtix_profile_fqdn" "url1" {
  name        = "fqdn2"
  description = "fqdn filter"
  fqdn_filter_list {
    fqdn_list = ["www.website1.com", "www.website2.com"]
    vendor_category_list {
      vendor     = "BRIGHTCLOUD"
      categories = ["Search Engines", "Reference and Research"]
    }
    policy               = "ALLOW_LOG"
    decryption_exception = false
  }
  fqdn_filter_list {
    fqdn_list = ["www.website3.com", "www.website4.com"]
    vendor_category_list {
      vendor     = "BRIGHTCLOUD"
      categories = ["Malware Sites", "Bot Nets", "Spyware and Adware"]
    }
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

* `fqdn_list` - (Optional) List of strings or regular expressions or predefined Categories
* `vendor_category_list` - (Optional) Predefined categories from Vendors. Please check [this section](#vendor-category-list) for the structure.
* `policy` - (Required) Action to take on the matching url (and method) "ALLOW_LOG", "ALLOW" (does not log the flow), "DENY_NOLOG" (does not log the flow), "DENY" (log the flow).
* `decryption_exception` - (Optional) true/false. In the proxy mode disable decryption and inspection of packets. Defaults to true.

## Vendor Category List

```
vendor_category_list {
  vendor     = "BRIGHTCLOUD"
  categories = ["Malware Sites", "Bot Nets", "Spyware and Adware"]
}
```

Please check the Valtix Dashboard for a list of predefined categories

## Attribute Reference

* `profile_id` - Id of the profile that can be referenced in other resources (e.g. valtix_policy_rules)
