# Resource: valtix_profile_urlfilter
Resource for creating and managing an FQDN Filtering Profile

## Example Usage
```hcl
resource "valtix_profile_fqdn" "fqdn1" {
  name        = "fqdn1"
  description = "FQDN filter list"
  fqdn_filter_list {
    fqdn_list = ["www.website1.com", ".*.website2.com"]
    vendor_category_list {
      vendor     = "BRIGHTCLOUD"
      categories = ["Search Engines", "Reference and Research"]
    }
    policy               = "Allow Log"
    decryption_exception = false
  }
  fqdn_filter_list {
    fqdn_list = ["www.website3.com", "www.website4.com"]
    vendor_category_list {
      vendor     = "BRIGHTCLOUD"
      categories = ["Malware Sites", "Bot Nets", "Spyware and Adware"]
    }
    policy               = "Deny Log"
    decryption_exception = false
  }
  uncategorized_fqdn_filter {
    policy               = "Deny Log"
    decryption_exception = false
  }
  default_fqdn_filter {
    policy               = "Deny No Log"
    decryption_exception = false
  }
}
```

## Argument Reference
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `no_fqdn_deny` - (Optional) Deny traffic when no FQDN found in packet. Applicable values: *true* or *false*.  Default value: *false*.
* `fqdn_filter_list` - (Required) One or more `fqdn_list` resources, where each resource is a row in the FQDN Filter List (maximum of 32 resources). Structure [defined below](#fqdn-filter-list).
* `uncategorized_fqdn_filter` - (Required) Uncategorized FQDN Filter action for any FQDN that does not match the FQDNs defined in the `fqdn_filter_list` resource and is not represented by any vendor category (whether specified or not). Structure [defined below](#uncategorized-fqdn-filter).
* `default_fqdn_filter` - (Required) Default FQDN Filter action for any FQDN that does not match the FQDNs defined in the `fqdn_filter_list` resource or is not matched by the `uncategorized_fqdn_filter` resource (if specified).  This should be the last resource specified in the list of resources. Structure [defined below](#default-fqdn-filter).

## FQDN Filter List
* `fqdn_list` - (Required) List of strings (maximum of 64 strings): Applicable values: FQDNs or Perl Compatible Regular Expression (PCRE) patterns.  Structure [defined below](#fqdn-list).
* `vendor_category_list` - (Optional) List of pre-defined Vendor Categories (maximum 128 categories).  Structure [defined below](#vendor-category-list). 
* `policy` - (Required) Action to take when an FQDN matches an entry in the `fqnd_list` or `vendor_category_list`.  Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event).
* `decryption_exception` - (Optional) When used in conjunction with a proxy Rule (ForwardProxy, ReverseProxy), instructs the proxy engine to bypass decryption. Applicable values: `true` or `false`.  If not specified, the default value is `true`.

## FQDN List
```
fqdn_list = ["www.website1.com", ".*.website2.com"]
```

## Vendor Category List
```
vendor_category_list {
  vendor     = "BRIGHTCLOUD"
  categories = ["Malware Sites", "Bot Nets", "Spyware and Adware"]
}
```

## Vendor Category List (All Categories)
```
vendor_category_list {
	vendor      = "BRIGHTCLOUD"
	categories  = ["Abortion","Abused Drugs","Adult and Pornography","Alcohol and Tobacco","Auctions","Bot Nets","Business and Economy","Cheating","Computer and Internet Info","Computer and Internet Security","Confirmed SPAM Sources","Content Delivery Networks","Cult and Occult","Dating","Dead Sites","Dynamically Generated Content","Educational Institutions","Entertainment and Arts","Fashion and Beauty","Financial Services","Gambling","Games","Government","Gross","Hacking","Hate and Racism","Health and Medicine","Home and Garden","Hunting and Fishing","Illegal","Image and Video Search","Individual Stock Advice and Tools","Internet Communications","Internet Portals","Job Search","Keyloggers and Monitoring","Kids","Legal","Local Information","Malware Sites","Marijuana","Military","Motor Vehicles","Music","News and Media","Nudity","Online Greeting Cards","Open HTTP Proxies","Parked Domains","Pay to Surf","Peer to Peer","Personal sites and Blogs","Personal Storage","Philosophy and Political Advocacy","Phishing and Other Frauds","Private IP Addresses","Proxy Avoidance and Anonymizers","Questionable","Real Estate","Recreation and Hobbies","Reference and Research","Religion","Search Engines","Sex Education","Shareware and Freeware","Shopping","Social Networking","Society","SPAM URLs","Sports","Spyware and Adware","Streaming Media","Swimsuits and Intimate Apparel","Training and Tools","Translation","Travel","Uncategorized","Unconfirmed SPAM Sources","Violence","Weapons","Web Advertisements","Web Hosting","Web-based Email"]
}
```

## Uncategorized FQDN Filter
```
uncategorized_fqdn_filter {
  policy               = "Deny Log"
  decryption_exception = false
}
```

## Default FQDN Filter
```
default_fqdn_filter {
  policy               = "Deny No Log"
  decryption_exception = false
}
```

Please check the Valtix UI (Manage -> Profiles -> FQDN Filtering) to obtain a list of predefined Categories.

## Attribute Reference
* `profile_id` - ID of the Profile that can be referenced in other resources (e.g., *valtix_policy_rules*)