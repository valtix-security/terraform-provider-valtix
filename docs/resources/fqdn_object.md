# Resource: valtix_fqdn_object
Resource for creating and managing an FQDN Object used by the Policy Rules for SNI matching 

## Example Usage

### Standalone FQDN Object
```hcl
resource "valtix_fqdn_object" "fqdn1" {
  name        = "fqdn1"
  description = "FQDN list"
  fqdn_match_list {
    fqdn_list = [
      "www\\.website1\\.com",
      ".*\\.website2\\.com"
    ]
    decryption_exception = false
  }
  fqdn_match_list {
    fqdn_list = [
      "www\\.website3\\.com",
      "www\\.website4\\.com"
    ]
    decryption_exception = false
  }
}
```

### Group FQDN Object
```hcl
resource "valtix_fqdn_object" "fqdn_group1" {
  name           = "fqdn_group1"
  description    = "FQDN group1"
  type           = "GROUP"
  group_member_ids = [
    valtix_fqdn_object.fqdn1.id,
    valtix_fqdn_object.fqdn2.id
  ]
}
```

## Argument Reference
* `name` - (Required) Name of the FQDN Object
* `description` - (Optional) Description of the FQDN Object
* `type` - (Optional) Specifies whether the FQDN Object is a Group FQDN Object.  The only applicable value is `GROUP`.  If not specified, the FQDN Object operates as Standalone (non-Group).
* `group_member_ids` - (Required - Group). Ordered list of FQDN Object (Standalone) IDs that make up the components of the FQDN Object (Group).  This argument only applies when `type` is set to `GROUP`.  The list can contain zero or more IDs and is limited to a maximum of 30 IDs.  The resulting aggregated FQDN Object is limited to a total of 254 FQDN List blocks.
* `fqdn_match_list` - (Required) One or more blocks, where each block is a row in the FQDN Object (maximum of 254 blocks). Structure [defined below](#fqdn-list).

## FQDN List
* `fqdn_list` - (Required) List of FQDNs (maximum of 60 FQDNs per list; maximum 2048 characters per FQDN). Applicable values are Perl Compatible Regular Expression (PCRE) patterns representing FQDNs.  When specifying a multi-level domain (e.g., `www.example.com`), it's important to escape the `.` character (e.g., `www\\.example\\.com`) otherwise it will be treated as a wildcard for any single character.  Structure [defined below](#fqdn-list).
* `decryption_exception` - (Optional) When used in conjunction with a proxy Rule (ForwardProxy, ReverseProxy), instructs the proxy engine to bypass decryption. Applicable values: `true` or `false`.  If not specified, the default value is `true`.

## FQDN List
```hcl
fqdn_list = [
  "www\\.website1\\.com",
  ".*\\.website2\\.com"
]
```

## Attribute Reference
* `id` - ID of the FQDN Object resource that can be referenced in other resources (e.g., *valtix_policy_rules*)

## Import
[*Public Preview*] FQDN Object resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_fqdn_object.fqdn1 10
```