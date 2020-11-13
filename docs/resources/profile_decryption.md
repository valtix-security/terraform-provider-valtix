# Resource: valtix_profile_decryption

Create a decryption profile that can be used in a service.

## Example Usage

```hcl
resource "valtix_profile_decryption" decryption_profile_1 {
  name             = "tls_profile_1"
  certificate_name = valtix_certificate.cert1.name
}
```

## Argument Reference

* `name` - (Required) Name of the decryption profile
* `description` - (Optional) Description of the profile
* `certificate_name` - (Required) Certificate name from the valtix_certificate object