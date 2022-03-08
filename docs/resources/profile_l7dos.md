# valtix_profile_l7dos
Resource for creating and managing an L7DoS Profile

## Example Usage
```hcl
resource "valtix_profile_l7dos" "l7dos1" {
  name        = "l7dos1"
  description = "pcap description"
  request_limits {
    target_uri           = "/api/resource1"
    requests_rate_block  = 1000
    burst_size           = 500
    rate_limited_methods = ["METHOD_GET", "METHOD_POST"]
  }
}
```

## Argument Reference
* `name` - (Required) Name of the IPS profile
* `description` - (Optional) Description of the IPS profile
* `request_limits` - (Required) List of URI and limits. Structure of limit is [defined below](#request-limit)

## Request Limit
* `target_uri` - (Required) URI to which the limits are applied. E.g '/api/resource1' or '/api/login'
* `requests_rate_block` - (Required) Number of requests/sec processed by Valtix. Depending on the number of instances running in the Gateway cluster, the backend application would get more traffic than the number specified here. This limit is per client_ip/src_ip
* `burst_size` - (Required) The queue size for client_ip/src_ip. Once the requests fill the burst_size and are still not processed, new requests from the same client would be dropped
* `rate_limited_methods` - (Required) HTTP actions/methods to which the rate limiting is applied. Valid values are `METHOD_GET`, `METHOD_POST`, `METHOD_PUT`, `METHOD_DELETE`, `METHOD_PATCH`, `METHOD_HEAD`, `METHOD_OPTIONS`

## Attribute Reference
* `profile_id` - ID of the Profile that can be referenced in other resources (e.g., *valtix_policy_rules*)