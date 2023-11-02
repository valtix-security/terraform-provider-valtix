# Resource: valtix_profile_decryption
Resource for creating and managing a Decryption Profile

## Example Usage
```hcl
resource "valtix_profile_decryption" "decryption_profile1" {
  name             = "decryption_profile1"
  certificate_name = valtix_certificate.cert1.name
}
```
## Argument Reference
* `name` - (Required) Name of the decryption profile
* `description` - (Optional) Description of the profile
* `certificate_name` - (Required) Name of certificate that is defined in the `valtix_certificate` resource that contains the desired certificate
* `min_tls_version` - (Optional) The minimum TLS version to use when establishing a secure frontend and backend connection when processing traffic via a forward or reverse proxy.  Applicable values are:  `TLS_VERSION_1_3`, `TLS_VERSION_1_2`, `TLS_VERSION_1_1`, `TLS_VERSION_1_0`.  If not specified, the default value is `TLS_VERSION_1_0`.  IMPORTANT: It is required to use the same value for `min_tls_version` in all Decryption Profiles that are used by Service Objects referenced by a Policy Ruleset or Policy Ruleset Group.  If different values are used, the value that will be applied cannot be predetermined.

## Attribute Reference
* `id` - ID of the Decryption Profile resource that can be referenced in other resources (e.g., *valtix_service_object*)

## Import
Decryption Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_decryption.decryption_profile1 10
```