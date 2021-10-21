# Resource: valtix_profile_urlfilter
Create URL Filtering Profile

## Example Usage
```hcl
resource "valtix_profile_urlfilter" "url1" {
  name        = "url1"
  description = "URL filter list"
  url_filter_list {
    url_list = ["www.website1.com", ".*.website2.com"]
    vendor_category_list {
      vendor     = "BRIGHTCLOUD"
      categories = ["Search Engines", "Reference and Research"]
    }
    policy    = "ALLOW_LOG"
  }
  url_filter_list {
    vendor_category_list {
      vendor     = "BRIGHTCLOUD"
      categories = ["Malware Sites", "Bot Nets", "Spyware and Adware"]
    }
    policy        = "DENY"
    return_status = 400
  }
  url_filter_list {
    url_list       = ["www.website3.com", "www.website4.com"]
    policy         = "DENY"
    filter_methods = ["POST"]
    return_status  = 400
  }
  default_url_filter {
    policy          = "DENY_NOLOG"
    return_status   = 500
  }
}
```

## Argument Reference
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `url_filter_list` - (Required) One or more *url_filter* resources, where each resource is a row in the URL filter list (maximum of 64 rows). Structure [defined below](#url-filter-list).
* `default_url_filter` - (Optional) Default behavior of URL filter. Structure [defined below](#url-filter).

## URL Filter List
* `url_list` - (Required) List of strings (maximum of 8 per row): URLs or Perl Compatible Regular Expression (PCRE) patterns.  Structure [defined below](#url-list).
* `vendor_category_list` - (Optional) List of pre-defined Vendor Categories (maximum of 150 per row).  Structure [defined below](#vendor-category-list). 
* `filter_methods` - (Optional) List of URL methods (e.g., GET, POST). Default (if unspecified) are all methods.
* `policy` - (Required) Action to take when a URL matches an entry in the *url_list* or *vendor_category_list*.  Applicable values are: "ALLOW_LOG" (log the event), "ALLOW" (do not log the event), "DENY" (log the event), "DENY_NOLOG" (do not log the event).  Events are viewed in the Valtix UI (Investigate -> Flow Analytics -> URL Filtering).
* `return_status` - (Optional) HTTP status code to return when URLs are denied.  Only applies to resources that have a *policy* specified as DENY or DENY_NOLOG.

## URL List
```
url_list = ["www.website1.com", ".*.website2.com"]
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

## Attribute Reference
* `profile_id` - ID of the Profile that can be referenced in other resources (e.g., *valtix_policy_rules*)
