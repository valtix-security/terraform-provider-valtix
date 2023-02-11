# Resource: valtix_profile_dlp
Resource for creating and managing a Data Loss Prevention (DLP) Profile

## Example Usage
```hcl
resource "valtix_profile_dlp" "dlp1" {
  name        = "dlp1"
  description = "DLP1 description"
  dlp_filter_list {
    description = "Routing Numbers"
    patterns = [
      "custom text to detect"
    ]
    static_patterns = [
      "Bank of America Routing Numbers - California"
    ]
    count  = 2
    action = "Deny Log"
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
* `action` - (Required) Action to take when a Data Loss Prevention (DLP) Network Threat is detected. Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event).

## Attribute Reference
* `id` - ID of the Data Loss Prevention (DLP) Profile resource that can be referenced in other resources (e.g., *valtix_policy_rules*)
* `profile_id` - (Deprecated) Same as the `id` attribute

## Import
[*Public Preview*] Data Loss Prevention (DLP) Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_dlp.dlp1 10
```