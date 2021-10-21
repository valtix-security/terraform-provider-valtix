# Resource: valtix_profile_dlp
Data loss prevention profile

## Example Usage
```hcl
resource "valtix_profile_dlp" "dlp1" {
  name        = "dlp1"
  description = "dlp1 description"
  dlp_filter_list {
    description = "Routing Numbers"
    patterns    = [
        "custom text to detect"
    ]
    static_patterns = [
      "Bank of America Routing Numbers - California"
    ]
    count       = 2
    action      = "DROP"
  }
}
```

## Argument Reference
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `dlp_filter_list` - (Required) A list of DLP Filters. Structure [defined below](#dlp-filter).

## DLP Filter
* `description` - (Required) Description of the Filter
* `patterns` - (Optional) List of custom Perl Compatible Regular Expression (PCRE) patterns 
* `static_patterns` - (Optional) List of pre-defined patterns
* `count` - (Required) Number of times the pattern must be seen before a match is determined
* `action` - (Required) Action to take when a match is detected. Events are viewed in the Valtix UI (Investigate -> Flow Analytics -> Network Threats). Valid values:
    * **ALERT** (Allow Log - log the event)
    * **DROP** (Deny Log - log the event)
    * **PASS** (Deny No Log - do not log the event)

## Attribute Reference
* `profile_id` - ID of the Profile that can be referenced in other resources (e.g., *valtix_policy_rules*)