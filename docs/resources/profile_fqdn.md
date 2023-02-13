# Resource: valtix_profile_fqdn
Resource for creating and managing an FQDN Filter/Match Profile

## Example Usage

### Standalone FQDN Filter Profile
```hcl
resource "valtix_profile_fqdn" "fqdn_filter1" {
  name        = "fqdn_filter1"
  description = "FQDN filter list"
  fqdn_filter_list {
    fqdn_list = [
      "www\\.website1\\.com",
      ".*\\.website2\\.com"
    ]
    vendor_category_list {
      vendor     = "BRIGHTCLOUD"
      categories = [
        "Search Engines",
        "Reference and Research"
      ]
    }
    policy               = "Allow Log"
    decryption_exception = false
  }
  fqdn_filter_list {
    fqdn_list = [
      "www\\.website3\\.com",
      "www\\.website4\\.com"
    ]
    vendor_category_list {
      vendor     = "BRIGHTCLOUD"
      categories = [
        "Malware Sites",
        "Bot Nets",
        "Spyware and Adware"
      ]
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

### Standalone FQDN Match Profile
```hcl
resource "valtix_profile_fqdn" "fqdn_match1" {
  name        = "fqdn_match1"
  description = "FQDN match list"
  mode        = "MATCH"
  fqdn_filter_list {
    fqdn_list = [
      "www\\.website1\\.com",
      ".*\\.website2\\.com"
    ]
    decryption_exception = false
  }
  fqdn_filter_list {
    fqdn_list = [
      "www\\.website3\\.com",
      "www\\.website4\\.com"
    ]
    decryption_exception = false
  }
}
```

### Group FQDN Filter Profile
```hcl
resource "valtix_profile_fqdn" "fqdn_filter_group" {
  name           = "fqdn_filter_group"
  description    = "FQDN filter group"
  type           = "GROUP"
  group_member_ids = [
    valtix_profile_fqdn.fqdn_filter1.id,
    valtix_profile_fqdn.fqdn_filter2.id
  ]
  uncategorized_fqdn_filter {
          policy               = "Deny No Log"
          decryption_exception = false
  }
  default_fqdn_filter {
          policy               = "Deny Log"
          decryption_exception = false
  }
}
```

### Group FQDN Match Profile
```hcl
resource "valtix_profile_fqdn" "fqdn_match_group" {
  name           = "fqdn_match_group"
  description    = "FQDN match group"
  type           = "GROUP"
  mode           = "MATCH"
  group_member_ids = [
    valtix_profile_fqdn.fqdn_match1.id,
    valtix_profile_fqdn.fqdn_match2.id
  ]
}
```

## Argument Reference
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `type` - (Optional) Specifies whether the FQDN Profile is a Group FQDN Profile.  The only applicable value is `GROUP`.  If not specified, the FQDN Profile operates as Standalone (non-Group).
* `mode` = (Optional) Specifies whether the FQDN Profile is a Filter Profile or Match Profile.  Applicable values are `FILTER` or `MATCH`.  If not specified, the default value is `FILTER`. This argument applies to Group and Standalone FQDN Profiles.
* `no_fqdn_deny` - (Optional) Deny traffic when no FQDN is found in the packet. Applicable values: `true` or `false`.  Default value: `false`.
* `group_member_ids` - (Required - Group). Ordered list of FQDN Filter Profile (Standalone) IDs that make up the components of the FQDN Filter Profile (Group).  This argument only applies when `type` is set to `GROUP`.  The list can contain zero or more IDs and is limited to a maximum of 30 IDs.  The resulting aggregated FQDN Filter Profile is limited to a total of 254 FQDN List blocks.
* `fqdn_child_ids` - (Deprecated) Same as the `group_member_ids` argument
* `fqdn_filter_list` - (Required) One or more blocks, where each block is a row in the FQDN Filter Profile (maximum of 254 blocks). Structure [defined below](#fqdn-list).
* `uncategorized_fqdn_filter` - (Required) Uncategorized FQDN Filter action for any FQDN that does not match the FQDNs defined in the `fqdn_filter_list` resource and is not represented by any vendor category (whether specified or not). This argument is required no matter the `type` specified, but only applies when the Profile operates as Standalone. When operating as part of a Group, the Group setting will apply. Structure [defined below](#uncategorized-fqdn-filter).
* `default_fqdn_filter` - (Required) Default FQDN Filter action for any FQDN that does not match the FQDNs defined in the `fqdn_filter_list` resource or is not matched by the `uncategorized_fqdn_filter` resource (if specified).  This should be the last resource specified in the list of resources. This argument is required no matter the `type` specified, but only applies when the Profile operates as Standalone. When operating as part of a Group, the Group setting will apply. Structure [defined below](#default-fqdn-filter).

## FQDN List
* `fqdn_list` - (Required) List of FQDNs (maximum of 60 FQDNs per list, combined with categories; maximum 2048 characters per FQDN). Applicable values are Perl Compatible Regular Expression (PCRE) patterns representing FQDNs.  When specifying a multi-level domain (e.g., `www.example.com`), it's important to escape the `.` character (e.g., `www\\.example\\.com`) otherwise it will be treated as a wildcard for any single character.  Structure [defined below](#fqdn-list).
* `vendor_category_list` - (Optional) List of pre-defined Vendor Categories (maximum 60 categories per list, combined with FQDNs).  Structure [defined below](#vendor-category-list). 
* `policy` - (Required) Action to take when an FQDN matches an entry in the `fqnd_list` or `vendor_category_list`.  Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event).
* `decryption_exception` - (Optional) When used in conjunction with a proxy Rule (ForwardProxy, ReverseProxy), instructs the proxy engine to bypass decryption. Applicable values: `true` or `false`.  If not specified, the default value is `true`.

## FQDN List
```hcl
fqdn_list = [
  "www\\.website1\\.com",
  ".*\\.website2\\.com"
]
```

## Vendor Category List
```hcl
vendor_category_list {
  vendor     = "BRIGHTCLOUD"
  categories = [
    "Malware Sites",
    "Bot Nets",
    "Spyware and Adware"
  ]
}
```

## Vendor Category List (All Categories)
```hcl
vendor_category_list {
	vendor      = "BRIGHTCLOUD"
	categories  = [
    "Abortion",
    "Abused Drugs",
    "Adult and Pornography",
    "Alcohol and Tobacco",
    "Auctions",
    "Bot Nets",
    "Business and Economy",
    "Cheating",
    "Computer and Internet Info",
    "Computer and Internet Security",
    "Confirmed SPAM Sources",
    "Content Delivery Networks",
    "Cult and Occult",
    "Dating",
    "Dead Sites",
    "Dynamically Generated Content",
    "Educational Institutions",
    "Entertainment and Arts",
    "Fashion and Beauty",
    "Financial Services",
    "Gambling",
    "Games",
    "Government",
    "Gross",
    "Hacking",
    "Hate and Racism",
    "Health and Medicine",
    "Home and Garden",
    "Hunting and Fishing",
    "Illegal",
    "Image and Video Search",
    "Individual Stock Advice and Tools",
    "Internet Communications",
    "Internet Portals",
    "Job Search",
    "Keyloggers and Monitoring",
    "Kids",
    "Legal",
    "Local Information",
    "Malware Sites",
    "Marijuana",
    "Military",
    "Motor Vehicles",
    "Music",
    "News and Media",
    "Nudity",
    "Online Greeting Cards",
    "Open HTTP Proxies",
    "Parked Domains",
    "Pay to Surf",
    "Peer to Peer",
    "Personal sites and Blogs",
    "Personal Storage",
    "Philosophy and Political Advocacy",
    "Phishing and Other Frauds",
    "Private IP Addresses",
    "Proxy Avoidance and Anonymizers",
    "Questionable",
    "Real Estate",
    "Recreation and Hobbies",
    "Reference and Research",
    "Religion",
    "Search Engines",
    "Sex Education",
    "Shareware and Freeware",
    "Shopping",
    "Social Networking",
    "Society",
    "SPAM URLs",
    "Sports",
    "Spyware and Adware",
    "Streaming Media",
    "Swimsuits and Intimate Apparel",
    "Training and Tools",
    "Translation",
    "Travel",
    "Unconfirmed SPAM Sources",
    "Violence",
    "Weapons",
    "Web Advertisements",
    "Web Hosting",
    "Web-based Email"
  ]
}
```

## Uncategorized FQDN Filter
```hcl
uncategorized_fqdn_filter {
  policy               = "Deny Log"
  decryption_exception = false
}
```

## Default FQDN Filter
```hcl
default_fqdn_filter {
  policy               = "Deny No Log"
  decryption_exception = false
}
```

Please check the Valtix UI (Manage -> Profiles -> FQDN Filtering) to obtain a list of predefined Categories.

## Attribute Reference
* `id` - ID of the FQDN Filter/Match Profile resource that can be referenced in other resources (e.g., *valtix_policy_rules*)
* `profile_id` - (Deprecated) Same as the `id` attribute

## Import
[*Public Preview*] FQDN Filter/Match (FQDN) Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_fqdn.fqdn1 10
```