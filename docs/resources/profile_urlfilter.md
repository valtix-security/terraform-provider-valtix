# Resource: valtix_profile_urlfilter
Resource for creating and managing a URL Filter (URL) Profile

## Example Usage
```hcl
resource "ciscomcd_profile_urlfilter" "url1" {
  name        = "url1"
  description = "URL filter list"
  url_filter_list {
    url_list = [
      "^(?i)(https?\:\/\/)([\da-z\.-]+\.)?(website1\.com)",
      "^(?i)(https?\:\/\/)([\da-z\.-]+\.)?(website2\.com)"
    ]
    policy = "Allow Log"
    filter_methods = ["GET"]
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
    return_status = 503
  }
  url_filter_list {
    vendor_category_list {
      vendor = "BRIGHTCLOUD"
      categories = [
        "Child Abuse Content"
      ]
    }
    policy = "Deny Log"
    return_status = 503
  }
  uncategorized_url_filter {
    policy        = "Deny Log"
    return_status = 500
  }
  default_url_filter {
    policy        = "Deny No Log"
    return_status = 500
  }
  deny_response = "The URL being accessed has been blocked for security reasons"
}
```

## Argument Reference

### URL Filter Profile Common Arguments
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `url_filter_list` - (Required) One or more blocks, where each block is a row in the URL Filter Profile (maximum of 254 blocks). Structure [defined below](#url-filter-list).
* `uncategorized_url_filter` - (Required) Uncategorized URL Filter action for any URL that does not match the URLs defined in the `url_filter_list` resource and is not represented by any vendor category (whether specified or not).  Structure [defined below](#uncategorized-url-filter).
* `default_url_filter` - (Required) Default URL Filter action for any URL that does not match the URLs defined in the `url_filter_list` resource or is not matched by the `uncategorized_url_filter` resource (if specified).  This should be the last resource specified in the list of resources. Structure [defined below](#default-url-filter).
* `deny_response` - (Optional) Specifies the HTTP response message to return back to the client when the URL is denied. This response is in addition to the `return_status` response code that is specified for each URL Filter List (`url_filter_list`) block.

### URL Filter List Arguments
* `url_list` - (Required) List of URLs (maximum of 60 URLs per list, combined with categories; maximum of 2048 characters per URL). Applicable values are Perl Compatible Regular Expression (PCRE) patterns representing FQDNs.  When specifying a multi-level domain (e.g., `www.example.com`), it's important to escape the `.` character (e.g., `www\\.example\\.com`) otherwise it will be treated as a wildcard for any single character.  Structure [defined below](#url-list).
* `vendor_category_list` - (Optional) List of pre-defined Vendor Categories (maximum of 60 categories per list, combined with URLs).  Structure [defined below](#vendor-category-list). 
* `filter_methods` - (Optional) List of URL methods (`DELETE`, `GET`, `HEAD`, `OPTIONS`, `PATCH`, `POST`, `PUT`). If not specified, the default value is `ALL`.
* `policy` - (Required) Action to take when a URL matches an entry in the `url_list` or `vendor_category_list`.  Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event).
* `return_status` - (Required) HTTP status code to return when URLs are denied.  This argument only applies to resources that have a `policy` set to `Deny Log` or `Deny No Log`.

### Vendor Category List Arguments
* `vendor` - (Required) Specifies the vendor to use for mapping domains to categories. Applicable values: `Brightcloud`. As of 24.10 Controller release, Valtix uses Talos as a vendor. Prior to 24.10 Controller release, Valtix used Brightcloud as a vendor. In order to retain backwards compatibility almongst Gateway releases, Brightcloud will remain as the vendor and the value `Brightcloud` will be required to be specified. Existing Profiles created and managed via Terraform can remain using the Brightcloud categories. However, if an existing Profile is changed in anyway, the Brightcloud category names will need be replaced by the equivalent Talos category names. In order to understand the Talos category names to use, go the UI, edit and save the Profile, export the Profile to Terraform and copy the Terraform code.
* `categories` - (Required) Specifies a list of one or more Talos categories to either allow or block domains that match against the categories.

### URL Filter List
```hcl
url_filter_list {
  url_list = [
    "^(?i)(https?\:\/\/)([\da-z\.-]+\.)?(website1\.com)",
    "^(?i)(https?\:\/\/)([\da-z\.-]+\.)?(website2\.com)"
  ]
  policy = "Allow Log"
  filter_methods = ["GET"]
}
url_filter_list {
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
  filter_methods = ["GET"]
  return_status  = 503
}
url_filter_list {
  vendor_category_list {
    vendor = "BRIGHTCLOUD"
    categories = [
      "Child Abuse Content"
    ]
  }
  policy = "Deny Log"
  filter_methods = ["GET"]
  return_status  = 503
}
```

### URL List
```hcl
url_list = [
  "^(?i)(https?\:\/\/)([\da-z\.-]+\.)?(website1\.com)",
  "^(?i)(https?\:\/\/)([\da-z\.-]+\.)?(website2\.com)"
]
```

### Vendor Category List (Malicious Categories)
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

### Vendor Category List (Child Abuse Categories)
```hcl
vendor_category_list {
  vendor     = "BRIGHTCLOUD"
  categories = [
    "Child Abuse Content"
  ]
}
```

### Uncategorized URL Filter
```hcl
uncategorized_url_filter {
  policy        = "Deny Log"
  return_status = 500
}
```

### Default URL Filter
```hcl
default_url_filter {
  policy        = "Deny No Log"
  return_status = 500
}
```

Please check the Valtix UI (Manage -> Profiles -> URL Filtering) to obtain a list of predefined Categories.

## Attribute Reference
* `id` - ID of the URL Filter Profile resource that can be referenced in other resources (e.g., *valtix_policy_rules*)

## Import
URL Filter (URL) Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_urlfilter.url1 10
```