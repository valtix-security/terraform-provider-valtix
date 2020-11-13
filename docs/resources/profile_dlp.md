# Resource: valtix_profile_dlp

Data loss prevention profile

## Example Usage

```hcl
resource "valtix_profile_dlp" dlp1 {
  name        = "dlp1"
  description = "dlp1 description"
  dlp_filter_list {
    description = "Routing Numbers"
    patterns = [
        "custom text to detect"
    ]
    static_patterns = [
      "Bank of America Routing Numbers - California"
    ]
    count  = 2
    action = "DROP"
  }
}
```

## Argument Reference

* `name` - (Required) Name of the profile
* `description` - (Optional) Description of the profile
* `dlp_filter_list` - (Required) A list of dlp filters. Structure is [defined below](#dlp-filter)

## DLP Filter
* `description` - (Required) Descrption of the filter
* `patterns` - (Optional) List of custom regular expression patterns 
* `static_patterns` - (Optional) List of predefined patterns
* `count` - (Required) Number of times the pattern must be seen before the data loss prevention functionality takes action
* `action` - (Required) action upon detecting the data loss prevention. Valid values:
    * **ALERT**
    * **DROP**
    * **PASS**