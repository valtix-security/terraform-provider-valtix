# Resource: valtix_profile_fqdn
Resource for creating and managing an FQDN Profile that can be used in a Policy Ruleset for Matching and Filtering purposes.

## Example Usage

### Standalone (Filter)
```hcl
resource "ciscomcd_profile_fqdn" "fqdn_filter1" {
  name        = "fqdn_filter1"
  description = "FQDN filter list"
  fqdn_filter_list {
    fqdn_list = [
      "www\\.website1\\.com",
      ".*\\.website2\\.com"
    ]
    policy               = "Allow Log"
    decryption_exception = false
  }
  fqdn_filter_list {
    fqdn_list = [
      "www\\.website3\\.com",
      "www\\.website4\\.com"
    ]
    policy               = "Deny Log"
    decryption_exception = false
  }
  fqdn_filter_list {
    vendor_category_list {
      vendor = "BRIGHTCLOUD"
      categories = [
        "Spyware and Adware",
        "Malware",
        "Botnets",
        "Open Mail Relay",
        "Poor Sender Reputation",
        "High Risk Sites and Locations",
        "Spam",
        "TOR Exit Nodes",
        "Mobile Threats",
        "Graymail",
        "Spoofing",
        "P2P Malware Node",
        "Open HTTP Proxy",
        "Lying DNS",
        "Newly Seen Domains",
        "DNS Tunneling",
        "Linkshare",
        "Cryptojacking",
        "Dynamic DNS",
        "Potential DNS Rebinding",
        "Bogon",
        "Exploits",
        "Malicious Sites",
        "Indicators of Compromise (IOC)",
        "Scam",
        "Ebanking Fraud",
        "Domain Generated Algorithm",
        "Phishing"
      ]
    }
    policy = "Deny Log"
  }
  fqdn_filter_list {
    vendor_category_list {
      vendor = "BRIGHTCLOUD"
      categories = [
        "Child Abuse Content"
      ]
    }
    policy = "Deny Log"
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

### Standalone (Match)
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
    cert_validation      = "Inherit from Decryption Profile"
  }
  fqdn_filter_list {
    fqdn_list = [
      "www\\.website3\\.com",
      "www\\.website4\\.com"
    ]
    decryption_exception = false
    cert_validation      = "Inherit from Decryption Profile"
  }
}
```

### Group (Filter)
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

### Group (Match)
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

### Common Arguments
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `type` - (Optional) Specifies whether the FQDN Profile is a Group FQDN Profile.  The only applicable value is `GROUP`.  If not specified, the FQDN Profile operates as Standalone (non-Group).
* `mode` = (Optional) Specifies whether the FQDN Profile is a Filter Profile or Match Profile.  Applicable values are `FILTER` or `MATCH`.  If not specified, the default value is `FILTER`. This argument applies to Group and Standalone FQDN Profiles.

### Group Arguments (Filter)
* `group_member_ids` - (Required - Group). Ordered list of FQDN Profile (Standalone) IDs that make up the components of the FQDN Profile (Group).  This argument only applies when `type` is set to `GROUP`.  The list can contain zero or more IDs and is limited to a maximum of 30 IDs.  The resulting aggregated FQDN Profile is limited to a total of 254 FQDN Profile Filter List blocks.
* `uncategorized_fqdn_filter` - (Required) Uncategorized FQDN Filter action for any FQDN that does not match the FQDNs defined in the `fqdn_filter_list` resource and is not represented by any vendor category (whether specified or not). This argument is required no matter the `type` specified, but only applies when the Profile operates as Standalone. When operating as part of a Group, the Group setting will apply. Structure [defined below](#uncategorized-fqdn-filter).
* `default_fqdn_filter` - (Required) Default FQDN Filter action for any FQDN that does not match the FQDNs defined in the `fqdn_filter_list` resource or is not matched by the `uncategorized_fqdn_filter` resource (if specified).  This should be the last resource specified in the list of resources. This argument is required no matter the `type` specified, but only applies when the Profile operates as Standalone. When operating as part of a Group, the Group setting will apply. Structure [defined below](#default-fqdn-filter).
* `no_fqdn_deny` - (Optional) Deny traffic when no FQDN is found in the packet. Applicable values: `true` or `false`.  If not specified, the default value is `false`.

### Group Arguments (Match)
* `group_member_ids` - (Required - Group). Ordered list of FQDN Profile (Standalone) IDs that make up the components of the FQDN Profile (Group).  This argument only applies when `type` is set to `GROUP`.  The list can contain zero or more IDs and is limited to a maximum of 30 IDs.  The resulting aggregated FQDN Profile is limited to a total of 254 FQDN Profile Filter List blocks.

### Standalone Arguments (Filter)
* `no_fqdn_deny` - (Optional) Deny traffic when no FQDN is found in the packet. Applicable values: `true` or `false`.  Default value: `false`.
* `fqdn_filter_list` - (Required) One or more blocks, where each block is a row in the FQDN Filter Profile (maximum of 254 blocks). Structure [defined below](#fqdn-filter-list).
* `uncategorized_fqdn_filter` - (Required) Uncategorized FQDN Filter action for any FQDN that does not match the FQDNs defined in the `fqdn_filter_list` resource and is not represented by any vendor category (whether specified or not). This argument is required no matter the `type` specified, but only applies when the Profile operates as Standalone. When operating as part of a Group, the Group setting will apply. Structure [defined below](#uncategorized-fqdn-filter).
* `default_fqdn_filter` - (Required) Default FQDN Filter action for any FQDN that does not match the FQDNs defined in the `fqdn_filter_list` resource or is not matched by the `uncategorized_fqdn_filter` resource (if specified).  This should be the last resource specified in the list of resources. This argument is required no matter the `type` specified, but only applies when the Profile operates as Standalone. When operating as part of a Group, the Group setting will apply. Structure [defined below](#default-fqdn-filter).

### Standalone Arguments (Match)
* `fqdn_filter_list` - (Required) One or more blocks, where each block is a row in the FQDN Filter Profile (maximum of 254 blocks). Structure [defined below](#fqdn-filter-list).

### Filter List Arguments (Filter)
* `fqdn_list` - (Required) List of FQDNs (maximum of 60 FQDNs per list, combined with categories; maximum 255 characters per FQDN). Applicable values are Perl Compatible Regular Expression (PCRE) patterns representing FQDNs.  When specifying a multi-level domain (e.g., `www.example.com`), it's important to escape the `.` character (e.g., `www\\.example\\.com`) otherwise it will be treated as a wildcard for any single character.  Structure [defined below](#fqdn-list).
* `vendor_category_list` - (Optional) List of pre-defined Vendor Categories (maximum 60 categories per list, combined with FQDNs).  Structure [defined below](#vendor-category-list). 
* `policy` - (Required) Action to take when an FQDN matches an entry in the `fqnd_list` or `vendor_category_list`.  Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event).
* `decryption_exception` - (Optional) When used in conjunction with a ForwardProxy Rule, instructs the proxy engine to bypass decryption. Applicable values: `true` or `false`.  If not specified, the default value is `true`.

### Filter List Arguments (Match)
* `fqdn_list` - (Required) List of FQDNs (maximum of 60 FQDNs per list; maximum 255 characters per FQDN). Applicable values are Perl Compatible Regular Expression (PCRE) patterns representing FQDNs.  When specifying a multi-level domain (e.g., `www.example.com`), it's important to escape the `.` character (e.g., `www\\.example\\.com`) otherwise it will be treated as a wildcard for any single character.  Structure [defined below](#fqdn-list).
proxy engine to bypass decryption. Applicable values: `true` or `false`.  If not specified, the default value is `true`.
* `decryption_exception` - (Optional) When used in conjunction with a ForwardProxy Rule, instructs the proxy engine to bypass decryption. Applicable values: `true` or `false`.  If not specified, the default value is `true`.
* `cert_validation` - (Optional) Specifies whether server certification validation will be performed, what action to take and whether to log the action.  The validation applies only to Forward Proxy policies and is performed by the Gateway for backend (Gateway to Server) TLS session negotiation.  This feature is available for Gateway versions 23.10 or later.  Applicable values: `Inherit from Decryption Profile` (use the configuration specified in the Decryption Profile), `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event).  If not specified, the default value is `Allow No Log`.  If not specified, the default value is `Inherit from Decryption Profile`.

### Vendor Category List Arguments (Filter)
* `vendor` - (Required) Specifies the vendor to use for mapping domains to categories. Applicable values: `Brightcloud`. As of 24.10 Controller release, Valtix uses Talos as a vendor. Prior to 24.10 Controller release, Valtix used Brightcloud as a vendor. In order to retain backwards compatibility almongst Gateway releases, Brightcloud will remain as the vendor and the value `Brightcloud` will be required to be specified. Existing Profiles created and managed via Terraform can remain using the Brightcloud categories. However, if an existing Profile is changed in anyway, the Brightcloud category names will need be replaced by the equivalent Talos category names. In order to understand the Talos category names to use, go the UI, edit and save the Profile, export the Profile to Terraform and copy the Terraform code.
* `categories` - (Required) Specifies a list of one or more Talos categories to either allow or block domains that match against the categories.

### FQDN Filter List (Filter)
```hcl
fqdn_filter_list {
  fqdn_list = [
    "www\\.website1\\.com",
    ".*\\.website2\\.com"
  ]
  policy = "Allow Log"
}
fqdn_filter_list {
  vendor_category_list {
    vendor = "BRIGHTCLOUD"
    categories = [
      "Bot Nets",
      "Phishing and Other Frauds",
      "Malware Sites",
      "Keyloggers and Monitoring",
      "Proxy Avoidance and Anonymizers",
      "Spyware and Adware",
      "SPAM URLs"
    ]
	}
  policy = "Deny Log"
}
```

### FQDN Filter List (Match)
```hcl
fqdn_filter_list {
  fqdn_list = [
    "www\\.website1\\.com",
    ".*\\.website2\\.com"
  ]
  policy = "Allow Log"
}
```

### FQDN List (Filter, Match)
```hcl
fqdn_list = [
  "www\\.website1\\.com",
  ".*\\.website2\\.com"
]
```

### Vendor Category List (Filter - Malicious Categories)
```hcl
vendor_category_list {
  vendor     = "BRIGHTCLOUD"
  categories = [
    "Spyware and Adware",
    "Malware",
    "Botnets",
    "Open Mail Relay",
    "Poor Sender Reputation",
    "High Risk Sites and Locations",
    "Spam",
    "TOR Exit Nodes",
    "Mobile Threats",
    "Graymail",
    "Spoofing",
    "P2P Malware Node",
    "Open HTTP Proxy",
    "Lying DNS",
    "Newly Seen Domains",
    "DNS Tunneling",
    "Linkshare",
    "Cryptojacking",
    "Dynamic DNS",
    "Potential DNS Rebinding",
    "Bogon",
    "Exploits",
    "Malicious Sites",
    "Indicators of Compromise (IOC)",
    "Scam",
    "Ebanking Fraud",
    "Domain Generated Algorithm",
    "Phishing"
  ]
}
```

### Vendor Category List (Filter - Child Abuse Categories)
```hcl
vendor_category_list {
  vendor     = "BRIGHTCLOUD"
  categories = [
    "Child Abuse Content"
  ]
}
```

### Uncategorized FQDN Filter (Filter)
```hcl
uncategorized_fqdn_filter {
  policy               = "Deny Log"
  decryption_exception = false
}
```

### Default FQDN Filter (Filter)
```hcl
default_fqdn_filter {
  policy               = "Deny No Log"
  decryption_exception = false
}
```

Please check the Valtix UI (Manage -> Profiles -> FQDN Filtering) to obtain a list of predefined Talos Categories.

## Attribute Reference
* `id` - ID of the FQDN Profile resource that can be referenced in other resources (e.g., *valtix_policy_rules*)

## Import
FQDN Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_fqdn.fqdn1 10
```