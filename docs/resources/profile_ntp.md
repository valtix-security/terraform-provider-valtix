# Resource: valtix_profile_ntp
Resource for creating and managing an NTP Profile. The Gateway uses NTP to synchronize its time. The NTP communcication uses the Gateway Management interface.  The NTP Profile allows a user to specify a set of custom NTP servers to use in place of the default servers.

## Example Usage
```hcl
resource "valtix_profile_ntp" "ntp1" {
  name = "NTP1"
  server_list = ["time.nist.gov", "pool.ntp.org"]
}
```

## Argument Reference
* `name` - (Required) Name of the NTP Profile
* `description` - (Optional) Description of the NTP Profile
* `server_list` - (Required) List of NTP servers to be applied to the Gateway NTP configuration

### Default NTP Servers
The default NTP servers that are configured on the Gateway for each CSP are as follows:

* **AWS:**  2.centos.pool.ntp.org, 169.254.169.123
* **Azure:** 0.centos.pool.ntp.org, 1.centos.pool.ntp.org, 2.centos.pool.ntp.org, 3.centos.pool.ntp.org
* **GCP:** metadata.google.internal
* **OCI:** 0.centos.pool.ntp.org, 1.centos.pool.ntp.org, 2.centos.pool.ntp.org, 3.centos.pool.ntp.org, 169.254.169.254

## Attribute Reference
* `id` - ID of the NTP Profile resource that can be referenced in other resources (e.g., *valtix_gateway*)

## Import
NTP Profile resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_profile_ntp.ntp1 10
```