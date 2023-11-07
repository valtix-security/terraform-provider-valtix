# Resource: valtix_profile_decryption
Resource for creating and managing a Decryption Profile

## Example Usage
```hcl
resource "valtix_profile_decryption" "decryption_profile1" {
  name             = "decryption_profile1"
  certificate_name = valtix_certificate.cert1.name
  min_tls_version  = "TLS_VERSION_1_3"
	cipher_suite_and_group {
		cipher_suite = "ECDHE_RSA_AES256_GCM_SHA384"
		cipher_suite_group = "PFS"
	}
	cipher_suite_and_group {
		cipher_suite = "DHE_RSA_AES256_GCM_SHA384"
		cipher_suite_group = "PFS"
	}
	cipher_suite_and_group {
		cipher_suite = "ECDHE_RSA_AES128_GCM_SHA256"
		cipher_suite_group = "PFS"
	}
	cipher_suite_and_group {
		cipher_suite = "DHE_RSA_AES128_GCM_SHA256"
		cipher_suite_group = "PFS"
	}
}
```
## Argument Reference
* `name` - (Required) Name of the decryption profile
* `description` - (Optional) Description of the profile
* `certificate_name` - (Required) Name of certificate that is defined in the `valtix_certificate` resource that contains the desired certificate
* `min_tls_version` - (Optional) The minimum TLS version to use when establishing a secure frontend and backend connection when processing traffic via a forward or reverse proxy.  Applicable values are:  `TLS_VERSION_1_3`, `TLS_VERSION_1_2`, `TLS_VERSION_1_1`, `TLS_VERSION_1_0`.  If not specified, the default value is `TLS_VERSION_1_0`.  IMPORTANT: It is required to use the same value for `min_tls_version` in all Decryption Profiles that are used by Service Objects referenced by a Policy Ruleset or Policy Ruleset Group.  If different values are used, the value that will be applied cannot be predetermined.
* `cert_validation` - (Optional) Specifies whether server certification validation will be performed, what action to take and whether to log the action.  The validation applies only to Forward Proxy policies and is performed by the Gateway for backend (Gateway to Server) TLS session negotiation.  This feature is available for Gateway versions 23.10 or later.  Applicable values: `Allow Log` (allow and log the event), `Allow No Log` (allow and do not log the event), `Deny Log` (deny and log the event), `Deny No Log` (deny and do not log the event).  If not specified, the default value is `Allow No Log`.
* `cipher_suite_and_group` - (Optional) Cipher Suite and Group block. This block can be repeated multiple times. See [Cipher Suite and Group](#cipher-suite-and-group) for the block structure.

## Cipher Suite and Group
Cipher Suite and Group are a set of cipher suites that can be included for TLS negotiation.  The specified set is in addition to the default recommended set.  Clients or servers that operate using older SSL/TLS libraries require use of less secure cipher suites.  It is not recommended to use older and less secure cipher suites, but if required, these suites can be enabled using the Cipher Suite and Group blocks.

The cipher suites that cannot be disabled are the most secure GCM-based PFS ciphers. All other suites can be enabled by including them in one or more Cipher Suite and Group blocks.

* `cipher_suite` - (Required) The name of the Cipher Suite to be included in the set of Cipher Suites to use for TLS negotiation.  Each Cipher Suite has a well-defined Cipher Suite Group name.
* `cipher_suite_group` - (Required) The name of the Cipher Suite Group.  Applicable values are `PFS`, `DIFFIE_HELLMAN`, and `PKCS_RSA`.

### Cipher Suite and Group Example
```hcl
cipher_suite_and_group {
	cipher_suite = "ECDHE_RSA_AES256_CBC_SHA384"
	cipher_suite_group = "PFS"
}
```

### PFS Cipher Suites
* ECDHE-RSA-AES256-GCM-SHA384
* ECDHE-RSA-AES256-CBC-SHA384
* DHE-RSA-AES256-GCM-SHA384
* DHE-RSA-AES128-GCM-SHA256
* ECDHE-RSA-AES128-GCM-SHA256
* DHE-RSA-AES256-CBC-SHA256
* DHE-RSA-AES256-CBC-SHA
* DHE-RSA-AES128-CBC-SHA256
* ECDHE-RSA-AES128-CBC-SHA256
* ECDHE-RSA-DES-CBC3-SHA
* ECDHE-RSA-RC4-SHA

### Diffie-Hellman Cipher Suites
* DH-RSA-AES256-GCM-SHA384
* DH-RSA-AES256-SHA2564
* DH-RSA-AES256-SHA4
* DH-RSA-AES128-GCM-SHA2564
* DH-RSA-AES128-SHA2564
* DH-RSA-AES128-SHA4

### PKCS (RSA) Cipher Suites
* AES256-GCM-SHA3844
* AES256-SHA256
* AES256-SHA
* AES128-GCM-SHA256
* AES128-SHA256
* AES128-SHA
* RC4-SHA
* RC4-MD5

## Attribute Reference
* `id` - ID of the Decryption Profile resource that can be referenced in other resources (e.g., *valtix_service_object*)

## Import
Decryption Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_decryption.decryption_profile1 10
```