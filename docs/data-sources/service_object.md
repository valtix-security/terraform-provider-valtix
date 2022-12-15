# DataSource: valtix_service_object
Data source for obtaining the attributes of a Service Object resource. The attributes can be used in a Policy Rules resource.

## Example Usage
```hcl
data "valtix_service_object" "app1_svc_https" {
  name = "app1-svc-https"
}

rule_set_id = valtix_policy_rule_set.ingress_policy_rule_set.id
  rule {
    name        = "tcp-443"
    description = "listen port 443 to target port 443 on app1"
    type        = "Forwarding"
    action      = "ALLOW_LOG"
    service     = data.valtix_service_object.app1_svc_https.id
    state       = "ENABLED"
  }
}
```

## Argument Reference
* `name` - (Required) Name of the Service Object resource

## Attributes Reference
* `id` - ID of the Service Object resource