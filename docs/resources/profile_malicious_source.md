# Resource: valtix_profile_malicious_source

Create Malicious Sources Profile

## Example Usage

```hcl
resource "valtix_profile_malicious_source" msrc1 {
  name                  = "msrc1"
  description           = "description"
  ip_reputation_enabled = true
  vendor_rule_set_name  = "1.0.0-08262020"
}
```

## Argument Reference

* `name` - (Required) Name of the IPS profile
* `description` - (Optional) Description of the IPS profile
* `ip_reputation_enabled` - (Optional) true/false
* `vendor_rule_set_name` - (Required) Vendor rule set name/version. Valid values are:
    * **1.0.0-08262020**
    * **1.0.0-08102020**
    * **1.0.0-07302020**
    * **1.0.0-07012020**
    * **1.0.0-06222020**
    * **1.0.0-05102020**