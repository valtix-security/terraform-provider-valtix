# Resource: valtix_service_object
Resource for creating and managing a Service Object.  A Service Object is used in a Policy Ruleset Rule to define how traffic will be matched and processed by the Rule.

~> **Note on SNI for a ReverseProxy Service Object**
  If multiple applications are accessed using the same port (e.g., port 443), then an SNI must be specified to differentiate the requests associated with each application.

~> **Note on Service Type for a ReverseProxy Service Object**
  If multiple ReverseProxy Service Objects that use the same port (e.g., port 443) are applied to the same Policy Ruleset, the Service Type (`service_type`) must be represented consistently by either an L7 proxy (`HTTP`, `HTTPS`, `WEBSOCKET`, `WEBSOCKET_S`) or an L4/L5 proxy (`TCP`, `UDP`, `TLS`).  L7 and L4/L5 proxies cannot be mixed for the same Policy Ruleset on the same port.  For further discussion or questions, please contact [Valtix Support](mailto:support@valtix.com).

## Example Usage

### ReverseProxy Examples
#### Listen on TCP/80 and proxy to HTTP/80 to the backend
```hcl
resource "valtix_service_object" "app1_svc_http" {
  name           = "app1-svc-http"
  description    = "App1 service: Listen on TCP/80 and proxy to HTTP/80"
  service_type   = "ReverseProxy"
  protocol       = "TCP"
  transport_mode = "HTTP"
  port {
    destination_ports = "80"
    backend_ports     = "80"
  }
  backend_address_group = valtix_address_object.app1_ag.id
}
```

#### Listen on TCP/443 TLS and proxy to HTTPS/443 to the backend
```hcl
resource "valtix_service_object" "app1_svc_https" {
  name           = "app1-svc-https"
  description    = "App1 service: Listen on TCP/443 and proxy to HTTPS/443"
  service_type   = "ReverseProxy"
  protocol       = "TCP"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
    backend_ports     = "443"
  }
  backend_address_group = valtix_address_object.app1_ag.id
  tls_profile           = valtix_profile_decryption.decryption_profile_1.id
}
```

#### Listen on TCP/443 TLS with an SNI and proxy to HTTPS/443 on the backend
```hcl
# Target to Address Object 1 using a list of SNIs
resource "valtix_service_object" "app1_svc_https" {
  name           = "app1-svc-https"
  description    = "App1 service: Listen on TCP/443 and proxy to HTTPS/443"
  service_type   = "ReverseProxy"
  protocol       = "TCP"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
    backend_ports     = "443"
  }
  sni                   = ["www.app1.com", "subdomain1.app1.com"]
  backend_address_group = valtix_address_object.app1_ag.id
  tls_profile           = valtix_profile_decryption.decryption_profile_1.id
}

# Target to Address Object 2 with a different list of SNIs
resource "valtix_service_object" "app2_svc_https" {
  name           = "app1-svc-https"
  description    = "App1 service: Listen on TCP/443 and target to HTTPS/443"
  service_type   = "ReverseProxy"
  protocol       = "TCP"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
    backend_ports     = "443"
  }
  sni                   = ["www.app2.com", "subdomain1.app2.com"]
  backend_address_group = valtix_address_object.app2_ag.id
  tls_profile           = valtix_profile_decryption.decryption_profile_2.id
}
```
For a complete set of arguments, see [ReverseProxy Arguments](#reverseproxy-arguments)

### ForwardProxy Examples
```hcl
resource "valtix_service_object" "internet_http" {
  name           = "internet-port-80"
  description    = "Allow port 80 to Internet"
  service_type   = "ForwardProxy"
  transport_mode = "HTTP"
  port {
    destination_ports = "80"
  }
}

resource "valtix_service_object" "internet_https" {
  name           = "internet-port-443"
  description    = "Allow port 443 to Internet"
  service_type   = "ForwardProxy"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
  }
  tls_profile = valtix_profile_decryption.decryption_profile1.id
}
```

For a complete set of arguments, see [ForwardProxy Arguments](#forwardproxy-arguments)

### Forwarding Examples
```hcl
resource "valtix_service_object" "forward_https_snat" {
  name         = "forward-https-snat"
  description  = "Forwarding 443 (HTTPS) SNAT"
  service_type = "Forwarding"
  protocol     = "TCP"
  port {
    destination_ports = "443"
  }
  source_nat = true
}
```

```hcl
resource "valtix_service_object" "forward_icmp_nosnat" {
  name         = "forward-icmp-nosnat"
  description  = "Forwarding ICMP No SNAT"
  service_type = "Forwarding"
  protocol     = "ICMP"
  source_nat   = false
}
```

For a complete set of arguments, see [Forwarding Arguments](#forwarding-arguments)

## Argument Reference

### Common Arguments
* `name` - (Required) Name of the Service Object
* `description` - (Optional) Description of the Service Object
* `service_type` - (Required) Type of Service Object.  Applicable values are `ReverseProxy`, `ForwardProxy`, or `Forwarding`.

### ReverseProxy Arguments
* `transport_mode` - (Required) Proxy to use for processing traffic.  Applicable values for secure proxy are `HTTPS`, `TLS`, or `WEBSOCKET_S`. Applicable values for non-secure proxy are `HTTP`, `TCP`, or `WEBSOCKET`.
* `tls_profile` - (Optional) Application certificate to issue and TLS settings to use when negotiating a secure TLS session between the client and Gateway.  Applicable value is a Decryption Profile (`valtix_profile_decryption`) ID.
* `client_tls_profile` - (Optional) Certificate to use to authenticate the client certificate in a mutual TLS handshake. Applicable value is a Decryption Profile (`valtix_profile_decryption`) ID.
* `port` - (Required) Destination port(s) to use for processing traffic.  Multiple ports or port ranges can be specified by repeating this block. Structure is [defined below](#reverseproxy-port-arguments).
* `protocol` - (Optional) Protocol to use for processing traffic.  Applicable values are `TCP` or `UDP`.  If not specified, the default value is `TCP`.
* `sni` - (Optional) SNIs to use as an L5 match to distinguish multiple TLS applications that use the same port.  Applicable values are one or more FQDN strings that are used to match against the TLS Hello header Service Name Indication (SNI).  The match will issue the corresponding Certificate defined by the `tls_profile` and establish a backend connection to the application defined by the `backend_address_group`. 
* `l7dos_profile` - (Optional) L7 DOS Profile to use to rate limit transactions.  Applicable value is an L7 DOS Profile (`valtix_profile_l7dos`) ID.
* `backend_address_group` - (Required) Server or load balancer to use for establishing the connection between the Gateway and server.  Applicable value is a Reverse Proxy Target Address Object (`valtix_address_object`) ID.

### ReverseProxy Port Arguments
This block can be specified multiple times to define a list of one or more Destination and Backend Ports.
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
* `destination_ports` - (Required) Destination port number(s) as a single port or a continuous range of ports (e.g, `80` or `80-100`)
* `backend_ports` - (Required) Target backend port number(s) specified as a single port or as a continuous range of ports (e.g, `80` or `80-100`).

~> **Note on Destination and Backend Port relationships**
  The number of ports specified in `destination_ports` and `backend_ports` must match, although the actual port numbers can be different.

For examples, see [ReverseProxy Examples](#reverseproxy-examples)

### ForwardProxy Arguments
* `transport_mode` - (Required) Proxy to use for processing traffic.  Applicable values for non-secure proxy are `HTTP` or `WEBSOCKET`.  Applicable values for secure proxy are `HTTPS` or `WEBSOCKET_S`.
* `port` - (Required) Destination port(s) to use for processing traffic.  Multiple ports or port ranges can be specified by repeating this block.  Structure is [defined below](#forwardproxy-port-arguments).
* `tls_profile` - (Optional) Intermediate, self-signed or enterprise-signed certificate to be issued and TLS settings to be used when negotiating a secure TLS session between the client and Gateway.  Applicable value is a Decryption Profile (`valtix_profile_decryption`) ID.

### ForwardProxy Port Arguments
This block can be specified multiple times to define a list of Destination Ports.
```hcl
port {
  destination_ports = "443"
}
```
* `destination_ports` - (Required) Destination port number(s) as a single port or a continuous range of ports (e.g, `80` or `80-100`)
<br><br>For examples, see [ForwardProxy Examples](#forwardproxy-examples)

### Forwarding Arguments
* `protocol` - (Optional) Protocol to use for processing traffic.  Applicable values are `TCP`, `UDP` or `ICMP`.  If not specified, the default value is `TCP`.
* `port` - (Required) Destination port(s) to use for processing traffic.  Multiple ports or port ranges can be specified by repeating this block.  Structure is [defined below](#forwarding-port-arguments).
* `source_nat` - (Optional) Specifies whether Source Network Address Translation (SNAT) should be performed on the session.  Applicable values are `true` or `false`. If not specified, the default value is `true`. (Even though this argument is optional, it is recommended to specify a value explicitly, as the default value may change in the future).

### Forwarding Port Arguments
This block can be specified multiple times to define a list of Destination Ports.
```hcl
port {
  destination_ports = "443"
}
```
* `destination_ports` - (Required) Destination port number(s) as a single port or a continuous range of ports (e.g, `80` or `80-100`)
<br><br>For examples, see [Forwarding Examples](#forwarding-examples)

## Attribute Reference
* `id` - ID of the Service Object resource that can be referenced in other resources (e.g., *valtix_policy_rules*)
* `service_id` - (Deprecated) Same as the `id` attribute

## Import
[*Public Preview*] Service Object resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_service_object.app1_svc_http 10
```