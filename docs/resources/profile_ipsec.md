# Resource: valtix_profile_ipsec
Resource for creating and managing a IP Security (IPSec) Profile

## Example Usage
```hcl
resource "valtix_profile_ipsec" "ipsec1" {
  name             = "ipsec1"
  description      = "IPSec1 profile"
  ike_dhgroup      = ["DHGROUP_14", "DHGROUP_15", "DHGROUP_16", "DHGROUP_19", "DHGROUP_20", "DHGROUP_21"]
  ike_integrity    = ["INTEGRITY_SHA256", "INTEGRITY_SHA"]
  ike_encryption   = ["IKE_ENCRYPTION_AES256", "IKE_ENCRYPTION_AES192", "IKE_ENCRYPTION_AES"]
  ike_prf          = ["PRF_SHA256"]
  ike_version      = "IKE_VERSION_V2"
  lifetime_seconds = 86400
  ipsec_mode       = "IPSEC_MODE_ESP"
  ipsec_encryption = ["IPSEC_ENCRYPTION_AES_GCM_256", "IPSEC_ENCRYPTION_AES_GCM_192", "IPSEC_ENCRYPTION_AES_GCM"]
  ipsec_integrity  = ["INTEGRITY_SHA256", "INTEGRITY_SHA"]
}
```

## Argument Reference
* `name` - (Required) Name of the Profile
* `description` - (Optional) Description of the Profile
* `ike_dhgroup` - (Required) Specifies the Diffie-Hellman (DH) group identifier, which the Internet Key Exchange (IKE) peers use to derive a shared secret without transmitting it to each other.  The higher the Diffie-Hellman group number, the greater the security. One or more values can be specified, where the highest negotiated group will be used. Appicable values: `DHGROUP_14`, `DHGROUP_15`, `DHGROUP_16`, `DHGROUP_19`, `DHGROUP_20`, `DHGROUP_21`.
* `ike_integrity` - (Required) Specifies the hash algorithm used to ensure data integrity for the Internet Key Exchange (IKE) protocol. It ensures that a packet comes from where it says it comes from and that it has not been modified in transit. One or more values can be specified where the highest negotiated integrity will be used. Applicable values: `INTEGRITY_SHA256`, `INTEGRITY_SHA`, `INTEGRITY_NULL`.
* `ike_encryption` - (Required) Specifies the symmetric encryption algorithm that protects the Internet Kehy Exchange (IKE) protocol. One or more values can be specified where the highest negotiated encryption will be used. Applicable values: `IKE_ENCRYPTION_AES256`, `IKE_ENCRYPTION_AES192`, `IKE_ENCRYPTION_AES`.
* `ike_prf` - (Required) Specifies the pseudo random function hash algorithm used to generate keying material. One or more values can be specified where the highest negotiated hash will be used. Applicable values: `PRF_SHA256`, `PRF_SHA`.
* `lifetime_seconds` - (Required) Specifies the SA lifetime. The default value is `86400` seconds or 24 hours. As a general rule, a shorter lifetime provides more secure ISAKMP negotiations (up to a point). However, with shorter lifetimes, the Gateway sets up future IPsec SAs more quickly.
* `ike_version` - (Required) Specifies the Internet Key Exchange (IKE) version. Applicable values: `IKE_VERSION_V1`, `IKE_VERSION_V2`.
* `ipsec_integrity` - (Required) Specifies the hash algorithm used to ensure data integrity of the data transmitted between two IPsec peers. It ensures that a packet comes from where it says it comes from and that it has not been modified in transit. One or more values can be specified where the highest negotiated integrity will be used. Applicable values: `INTEGRITY_SHA256`, `INTEGRITY_SHA`, `INTEGRITY_NULL`.
* `ipsec_encryption` - (Required) Specifies the symmetric encryption algorithm that protects data transmitted between two IPsec peers. One or more values can be specified where the highest negotiated encryption will be used. Applicable values: `IPSEC_ENCRYPTION_AES_GCM_256`, `IPSEC_ENCRYPTION_AES_GCM_192`, `IPSEC_ENCRYPTION_AES_GCM`.
* `ipsec_mode` - (Required) Specifies the protocol used to encrypt and authenticates data packets between IPsec peers. Applicable values: `IPSEC_MODE_ESP`, `IPSEC_MODE_AH`.

## Attribute Reference
* `id` - ID of the IP Security (IPSec) Profile resource that can be referenced in other resources (e.g., *valtix_vpn_connections*)

## Import
IP Security (IPSec) Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_ipsec.ipsec1 10
```