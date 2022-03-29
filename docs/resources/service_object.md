# Resource: valtix_service_object
Resource for creating and managing a Service Object.  A Service Object is used in a Policy Ruleset Rule to define how the traffic will be processed by the Rule.

~> **Note on valtix_service_object ReverseProxy resource**
If multiple applications are accessed using the same port (e.g., port 443), then an SNI must be specified to differentiate the requests associated with each application.

## Example Usage

### ReverseProxy Examples
#### Listen on TCP/80 and proxy to HTTP/80 to the backend
```hcl
resource "valtix_service_object" "app1-svc-http" {
  name           = "app1-svc-http"
  description    = "app1 service: listen on TCP/80 and proxy to HTTP/80"
  service_type   = "ReverseProxy"
  protocol       = "TCP"
  transport_mode = "HTTP"
  port {
    destination_ports = "80"
    backend_ports     = "80"
  }
  backend_address_group = valtix_address_object.app1-ag.address_id
}
```

#### Listen on TCP/443 TLS and proxy to HTTPS/443 to the backend
```hcl
resource "valtix_service_object" "app1-svc-https" {
  name           = "app1-svc-https"
  description    = "app1 service: listen on TCP/443 and proxy to HTTPS/443"
  service_type   = "ReverseProxy"
  protocol       = "TCP"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
    backend_ports     = "443"
  }
  backend_address_group = valtix_address_object.app1-ag.address_id
  tls_profile           = valtix_profile_decryption.decryption_profile_1.profile_id
}
```

#### Listen on TCP/443 TLS with an SNI and proxy to HTTPS/443 on the backend
```hcl
# Target to Address Object 1 using a list of SNIs
resource "valtix_service_object" "app1-svc-https" {
  name           = "app1-svc-https"
  description    = "app1 service: listen on TCP/443 and proxy to HTTPS/443"
  service_type   = "ReverseProxy"
  protocol       = "TCP"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
    backend_ports     = "443"
  }
  sni                   = ["www.app1.com", "subdomain1.app1.com"]
  backend_address_group = valtix_address_object.app1-ag.address_id
  tls_profile           = valtix_profile_decryption.decryption_profile_1.profile_id
}

# Target to Address Object 2 with a different list of SNIs
resource "valtix_service_object" "app2-svc-https" {
  name           = "app1-svc-https"
  description    = "app1 service: listen on TCP/443 and target to HTTPS/443"
  service_type   = "ReverseProxy"
  protocol       = "TCP"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
    backend_ports     = "443"
  }
  sni                   = ["www.app2.com", "subdomain1.app2.com"]
  backend_address_group = valtix_address_object.app2-ag.address_id
  tls_profile           = valtix_profile_decryption.decryption_profile_2.profile_id
}
```
For a complete set of arguments, see [ReverseProxy Arguments](#reverseproxy-arguments)

### ForwardProxy Examples
```hcl
resource "valtix_service_object" "internet-http" {
  name           = "internet-port-80"
  description    = "allow port 80 to Internet"
  service_type   = "ForwardProxy"
  transport_mode = "HTTP"
  port {
    destination_ports = "80"
  }
}

resource "valtix_service_object" "internet-https" {
  name           = "internet-port-443"
  description    = "allow port 443 to Internet"
  service_type   = "ForwardProxy"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
  }
  tls_profile = valtix_profile_decryption.decryption_profile_2.profile_id
}
```

For a complete set of arguments, see [ForwardProxy Arguments](#forwardproxy-arguments)

### Forwarding Example
```hcl
resource "valtix_service_object" "forward-https" {
  name         = "forward-https"
  service_type = "Forwarding"
  port {
    destination_ports = "443"
  }
  source_nat = true
}
```

For a complete set of arguments, see [Forwarding Arguments](#forwarding-arguments)

## ReverseProxy Arguments
* `name` - (Required) Name of the service object
* `description` - (Optional) Description of the service object
* `service_type` - (Required) `ReverseProxy`
* `protocol` - (Optional) `TCP` or `UDP`. `TCP` is default. This is the listener protocol.
* `transport_mode` - (Required) If tls_profile is specified, then the valid values are `HTTPS`, `TLS`, `WEBSOCKET_S`. If tls_profile is not specified, then valid values are `HTTP`, `TCP`, `WEBSOCKET`. The protocol used by the Gateway to communicate with the backend application defined using a Reverse Proxy Target Address Object (`backend_address_group`)
* `port` - (Required) This can be specified multiple times if the service runs on multiple ports. Structure is [documented below](#reverseproxy-port)
* `sni` - (Optional) List of FQDN strings that are evaluated by the Gateway to determine a Rule match.  The match will issue the corresponding Certificate defined by the `tls_profile` and establish a backend connection to the application defined by the `backend_address_group`. This argument is used to distinguish multiple TLS applications that use the same port.
* `tls_profile` - (Optional) Decryption profile ID
* `l7dos_profile` - (Optional) L7 DOS profile ID
* `client_tls_profile` - (Optional) Decryption profile ID. This profile is used to authenticate client certificate in TLS handshake

### ReverseProxy Port Arguments
This block can be specified multiple times to define a list of one or more listener ports.
```hcl
port {
  destination_ports = "443"
  backend_ports     = "443"
}

# If the listen ports are continuous and target ports are continuous
port {
  destination_ports = "80-100"
  backend_ports     = "8000-8020"
}
```
* `destination_ports` - (Required) Destination port number as a string or a continuous range of destination port numbers (e.g, `80` or `80-100`)
* `backend_ports` - (Required) Backend (target) port number as a string or a continuous range of target port numbers (e.g, `80` or `80-100`). The port specified in `destination_ports` and `backend_ports` must match.
<br><br>For examples, see [ReverseProxy Examples](#reverseproxy-examples)

### ForwardProxy Arguments
* `name` - (Required) Name of the Service Object
* `description` - Description of the Service Object
* `service_type` - (Required) `ForwardProxy`
* `transport_mode` - (Required) `HTTP`, `HTTPS`
* `port` - (Required) This argument can be specified multiple times if the service can run on multiple ports. Structure is [documented below](#forwardproxy-port)
* `tls_profile` - (Optional) Decryption Profile ID.

### ForwardProxy Port Arguments
This block can be specified multiple times to define a list of listener ports.
```hcl
port {
  destination_ports = "443"
}
```
* `destination_ports` - (Required) Destination port number as a string or a continuous range of destination port numbers (e.,g `80` or `80-100`)
<br><br>For examples, see [ForwardProxy Examples](#forwardproxy-examples)

### Forwarding Arguments
* `name` - (Required) Name of the service object
* `description` - Description of the service object
* `service_type` - (Required) `Forwarding`
* `protocol` - (Optional) `TCP` or `UDP`. If not specified, the default value is `TCP`.
* `port` - (Required) This can be specified multiple times if the service runs on multiple ports. Structure is [documented below](#forwardproxy-port)
* `source_nat` - (Optional) `true` or `false`. Specifies whether source NAT (Source Network Address Translation - SNAT) should be performed on the session.  If not specified, the default value is `false`.(Even though this is optional, it is recommended to specify a value explicitly, as the default value may change in the future)
<br><br>For an example, see [Forwarding Example](#forwarding-example)

## Attribute Reference
* `service_id` - ID of the Service Object that can be referenced in other resources (e.g., *valtix_policy_rules*)