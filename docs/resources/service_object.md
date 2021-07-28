# valtix_service_object

Service objects represent the backend application and how it's serviced to the external users.
Valtix gateway acts as a proxy to this service and all the end users' access to the application
goes via the gateway. The service defines a listen port where the gateway listens and specifies
the target ip/fqdn and port number to which the end users' requests are forwarded. If the listen
port must be TLS encrypted, a decryption profile with a certificate must be provided.
The transport to the backend can be a HTTP, HTTPS, TCP or TLS session. Please look into the
documentation for the details of each of these types.
If multiple services (backends) need to listen on the same port (for e.g port 443), then an SNI
must be provided to differentiate the requests.

## Example Usage

### ReverseProxy: Listen on port 80 and transport to port 80 on backend

```hcl
resource "valtix_service_object" app1-svc {
  name           = "app1-svc-http"
  description    = "app1 service: listen on 80 and target to 80"
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

### ReverseProxy: Listen on port 443 TLS and transport to port 443 HTTPS on backend

```hcl
resource "valtix_service_object" app1-svc-https {
  name           = "app1-svc-https"
  description    = "app1 service: listen on 443 and target to 443"
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

### ReverseProxy: Listen on port 443 TLS and transport to port 443 HTTPS on backend with SNI **

```hcl
resource "valtix_service_object" app1-svc-https {
  name           = "app1-svc-https"
  description    = "app1 service: listen on 443 and target to 443"
  service_type   = "ReverseProxy"
  protocol       = "TCP"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
    backend_ports     = "443"
  }
  sni                   = ["www.app1.com", "subodmain1.app1.com"]
  backend_address_group = valtix_address_object.app1-ag.address_id
  tls_profile           = valtix_profile_decryption.decryption_profile_1.profile_id
}

# Target to address object 2 with a different SNI. address and decryption are
# provided in the example as a reference but the definition of
# address object and decryption profile is not provided here and assumed to be
# defined as describe earlier.

resource "valtix_service_object" app2-svc-https {
  name           = "app1-svc-https"
  description    = "app1 service: listen on 443 and target to 443"
  service_type   = "ReverseProxy"
  protocol       = "TCP"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
    backend_ports     = "443"
  }
  sni                   = ["www.app2.com", "subodmain1.app2.com"]
  backend_address_group = valtix_address_object.app2-ag.address_id
  tls_profile           = valtix_profile_decryption.decryption_profile_2.profile_id
}
```

### Forward Proxy (Egress) Service object

```hcl
resource "valtix_service_object" internet-http {
  name           = "internet-port-80"
  description    = "allow port 80 to internet"
  service_type   = "ForwardProxy"
  transport_mode = "HTTP"
  port {
    destination_ports = "80"
  }
}

resource "valtix_service_object" internet-https {
  name           = "internet-port-443"
  description    = "allow port 443 to internet"
  service_type   = "ForwardProxy"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
  }
  tls_profile = valtix_profile_decryption.decryption_profile_2.profile_id
}
```

### Forwarding Service object

```hcl
resource "valtix_service_object" forward-https {
  name           = "Forward-HTTPS"
  service_type   = "Forwarding"
  port {
    destination_ports = "443"
  }
}
```

## Argument Reference

## ReverseProxy

* `name` - (Required) Name of the service object
* `description` - (Optional) Description of the service object
* `service_type` - (Required) "ReverseProxy", "ForwardProxy" or "Forwarding"
* `protocol` - (Optional) "TCP" or "UDP". "TCP" is default. This is the listener protocol.
* `transport_mode` - (Required) "HTTP", "HTTPS", "TCP", "TLS". The protocol used by the gateway to communicate with the backend.
* `port` - (Required) This can be specified multiple times if the service runs on multiple ports. Structure is [documented below](#reverseproxy-port)
* `sni` - (Optional) List of FQDN strings that's matched by GW to find the destination (target) address group. Used to distinguish multiple TLS applications on a single port
* `tls_profile` - (Optional) Decryption profile id
* `l7dos_profile` - (Optional) L7 DOS profile id

## ReverseProxy Port

port can be specified multiple times to define a list of ports that the service can listen.

```hcl
port {
  destination_ports = "443"
  backend_ports = "443"
}

# if the listen ports are continuous and target ports are continuous

port {
  destination_ports = "80-100"
  backend_ports = "8000-8020"
}
```

* `destination_ports` - (Required) Destination port number as a string or a continuous range of destination port numbers (e.g "80" or "80-100")
* `backend_ports` - (Required) Backend (target) port number as a string or a continuous range of target port numbers (e.g "80" or "80-100"). The range count in destination and backend must match.


## ForwardProxy

* `name` - (Required) Name of the service object
* `description` - Description of the service object
* `service_type` - (Required) "ForwardProxy"
* `transport_mode` - (Required) "HTTP", "HTTPS"
* `port` - (Required) This can be specified multiple times if the service can run on multiple ports. Structure is [documented below](#forwardproxy-port)
* `tls_profile` - (Optional) Decryption profile id.

## Forwarding

* `name` - (Required) Name of the service object
* `description` - Description of the service object
* `service_type` - (Required) "Forwarding"
* `protocol` - (Optional) "TCP" or "UDP". "TCP" is default.
* `port` - (Required) This can be specified multiple times if the service can run on multiple ports. Structure is [documented below](#forwardproxy-port)
* `source_nat` - (Optional) true or false. Specifies whether source NAT (Network Address Translation) would be performed on the packet flow


## ForwardProxy Port
port can be specified multiple times to define a list of ports that the service can listen.

```hcl
port {
  destination_ports = "443"
}
```

* `destination_ports` - (Required) Destination port number as a string or a continuous range of destination port numbers (e.g "80" or "80-100")

## Attribute Reference

* `service_id` - Id of the service object that can be referenced in other resources (e.g. valtix_policy_rules)
