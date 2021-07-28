# Resource: valtix_profile_urlfilter

Create URL Filtering Profile

## Example Usage

```hcl
resource "valtix_profile_urlfilter" url1 {
  name        = "url1"
  description = "predefined rules tagged as 'connectivity'"
  url_filter_list {
    url    = "https://www.website1.com"
    policy = "ALLOW_LOG"
  }
  url_filter_list {
    url           = "https://www.website2.com"
    policy        = "DENY_NOLOG"
    return_status = 400
  }
  url_filter_list {
    url            = "https://www.website3.com"
    policy         = "DENY_NOLOG"
    filter_methods = ["POST"]
    return_status  = 400
  }
  default_url_filter {
    policy        = "DENY_NOLOG"
    return_status = 500
  }
}
```

## Argument Reference

* `name` - (Required) Name of the profile
* `description` - (Optional) Description of the profile
* `url_filter_list` - (Required) List of url_filter resources. Structure [defined below](#url-filter)
* `default_url_filter` - (Optional) Default behavior of URL filter. Structure [defined below](#url-filter)

## URL Filter

* `url` - (Required) String or regular expression or a predefined Category
* `filter_methods` - (Optional) URL Methods (GET, POST etc). Default all the methods
* `policy` - (Required) Action to take on the matching url (and method) "ALLOW_LOG", "ALLOW" (does not log the flow), "DENY_NOLOG" (does not log the flow), "DENY" (log the flow).
* `return_status` - (Optional) HTTP status code to return for DENY and DENY_NOLOG policy

## Attribute Reference

* `profile_id` - Id of the profile that can be referenced in other resources (e.g. valtix_policy_rules)
