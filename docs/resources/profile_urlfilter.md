# Resource: valtix_profile_urlfilter
Resource for creating and managing a URL Filtering Profile

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
    policy = "Allow Log"
  }
  url_filter_list {
    vendor_category_list {
      vendor     = "BRIGHTCLOUD"
      categories = ["Malware Sites", "Bot Nets", "Spyware and Adware"]
    }
    policy        = "Deny Log"
    return_status = 400
  }
  url_filter_list {
    url_list       = ["www.website3.com", "www.website4.com"]
    policy         = "Deny Log"
    filter_methods = ["POST"]
    return_status  = 400
  }
  default_url_filter {
    policy        = "Deny No Log"
    return_status = 500
  }
}
```

## Argument Reference
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `url_filter_list` - (Required) One or more `url_list` resources, where each resource is a row in the URL Filter List (maximum of 64 resources). Structure [defined below](#url-filter-list).
* `default_url_filter` - (Optional) Default URL Filter behavior for any URL that does not match the URLs defined in the `url_filter_list` resource.  This should be the last resource specified in the list of resources. Structure [defined below](#url-filter-list).

## URL Filter List
* `url_list` - (Required) List of strings (maximum of 64 strings). Applicable values: URLs or Perl Compatible Regular Expression (PCRE) patterns. Structure [defined below](#url-list).
* `vendor_category_list` - (Optional) List of pre-defined Vendor Categories (maximum of 128 categories).  Structure [defined below](#vendor-category-list). 
* `filter_methods` - (Optional) List of URL methods (`ALL`, `DELETE`, `GET`, `HEAD`, `OPTIONS`, `PATCH`, `POST`, `PUT`). If not specified, the default value is `ALL`.
* `policy` - (Required) Action to take when a URL matches an entry in the `url_list` or `vendor_category_list`.  Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event).
* `return_status` - (Optional) HTTP status code to return when URLs are denied.  This argument only applies to resources that have a `policy` set to `Deny Log` or `Deny No Log`.

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