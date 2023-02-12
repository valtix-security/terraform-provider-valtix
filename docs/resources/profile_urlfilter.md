# Resource: valtix_profile_urlfilter
Resource for creating and managing a URL Filtering (URL) Profile

## Example Usage
```hcl
resource "valtix_profile_urlfilter" "url1" {
  name        = "url1"
  description = "URL filter list"
  url_filter_list {
    url_list = [
      "https://.*\\.website1\\.com/.*",
      "https://.*\\.website2\\.com/.*"
    ]
    policy = "Allow Log"
    filter_methods = ["GET"]
  }
  
  url_filter_list {
    url_list       = [
      "https://.*\\.website1\\.com/.*",
      "https://.*\\.website2\\.com/.*" 
    ]
    policy         = "Deny Log"
    filter_methods = ["POST"]
    return_status  = 400
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
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `url_filter_list` - (Required) One or more blocks, where each block is a row in the URL Filtering Profile (maximum of 254 blocks). Structure [defined below](#url-list).
* `uncategorized_url_filter` - (Required) Uncategorized URL Filter action for any URL that does not match the URLs defined in the `url_filter_list` resource and is not represented by any vendor category (whether specified or not).  Structure [defined below](#uncategorized-url-filter).
* `default_url_filter` - (Required) Default URL Filter action for any URL that does not match the URLs defined in the `url_filter_list` resource or is not matched by the `uncategorized_url_filter` resource (if specified).  This should be the last resource specified in the list of resources. Structure [defined below](#default-url-filter).
* `deny_response` - (Optional) Specifies the HTTP response message to return back to the client when the URL is denied. This response is in addition to the `return_status` response code that is specified for each URL Filter List (`url_filter_list`) block.

## URL List
* `url_list` - (Required) List of URLs (maximum of 60 URLs per list, combined with categories; maximum of 2048 characters per URL). Applicable values are Perl Compatible Regular Expression (PCRE) patterns representing FQDNs.  When specifying a multi-level domain (e.g., `www.example.com`), it's important to escape the `.` character (e.g., `www\\.example\\.com`) otherwise it will be treated as a wildcard for any single character.  Structure [defined below](#url-list).
* `vendor_category_list` - (Optional) List of pre-defined Vendor Categories (maximum of 60 categories per list, combined with URLs).  Structure [defined below](#vendor-category-list). 
* `filter_methods` - (Optional) List of URL methods (`ALL`, `DELETE`, `GET`, `HEAD`, `OPTIONS`, `PATCH`, `POST`, `PUT`). If not specified, the default value is `ALL`.
* `policy` - (Required) Action to take when a URL matches an entry in the `url_list` or `vendor_category_list`.  Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event).
* `return_status` - (Optional) HTTP status code to return when URLs are denied.  This argument only applies to resources that have a `policy` set to `Deny Log` or `Deny No Log`.

## URL List
```hcl
url_list = [
  "https://.*\\.website1\\.com/.*",
  "https://.*\\.website2\\.com/.*"
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

## Uncategorized URL Filter
```hcl
uncategorized_url_filter {
  policy        = "Deny Log"
  return_status = 500
}
```

## Default URL Filter
```hcl
default_url_filter {
  policy        = "Deny No Log"
  return_status = 500
}
```

## Attribute Reference
* `id` - ID of the URL Filtering Profile resource that can be referenced in other resources (e.g., *valtix_policy_rules*)
* `profile_id` - (Deprecated) Same as the `id` attribute

## Import
[*Public Preview*] URL Filtering (URL) Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_urlfilter.url1 10
```