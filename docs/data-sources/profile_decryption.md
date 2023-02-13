# DataSource: valtix_decryption_profile
Data source for obtaining attributes of a Decryption Profile resource.  The attributes can be used in the arguments of a Service Object resource.

## Example Usage
```hcl
data "valtix_profile_decryption" "decryption_profile1" {
  name = "decryption_profile1"
}

resource "valtix_service_object" "internet_https" {
  name           = "internet-port-443"
  description    = "allow port 443 to Internet"
  service_type   = "ForwardProxy"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
  }
  tls_profile = data.valtix_profile_decryption.decryption_profile1.id
}
```

## Argument Reference
* `name` - (Required) Name of the Decryption Profile resource

## Attributes Reference
* `id` - ID of the Decryption Profile resource