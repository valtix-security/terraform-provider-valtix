# DataSource: valtix_gateway
Data source for obtaining attributes of a Gateway resource

## Example Usage
```hcl
data "valtix_gateway" "aws-gw1" {
  name = "aws-gw1"
}
```

## Argument Reference
* `name` - (Required) Name of the Gateway resource

## Attributes Reference
* `id` - ID of the Gateway resource
* `gateway_gwlb_endpoints` - (AWS only) AWS Gateway Load Balancer endpoints created in each of the AZs displayed in the format as follows:

    ```hcl
    gateway_gwlb_endpoints {
        endpoint_id          = "vpce-047c749fc6f7e0c0d"
        network_interface_id = "eni-017eacdb23d2ebaf4"
        subnet_id            = "subnet-0d61750e97caafd9d"
    }
    gateway_gwlb_endpoints {
        endpoint_id          = "vpce-0707fa3f03c5064a7"
        network_interface_id = "eni-020464bd838461bca"
        subnet_id            = "subnet-0fd61e07f200224f1"
    }
    ```

* `gwlb_service_name` - (AWS only) VPC Endpoint Service Name associated with the AWS Gateway Load Balancer.  This name can be used by the AWS Terraform Provider for establishing a GWLB Endpoint connection.

* `gwlb_service_id` - (AWS only) VPC Endpoint Service ID associated with the AWS Gateway Load Balancer.  This ID can be used by the AWS Terraform Provider for accepting GWLB Endpoint connections and assigning Principals.

* `gateway_endpoint` - For Gateways of `security_type = INGRESS`, this represents the NLB endpoint (FQDN, IP Address) to be used as the target for the client communicating with any application protected by the Valtix Ingress Gateway.  This information is most often used in a DNS A record (IP Address) or CNAME record (FQDN) to resolve the application FQDN to the Valtix Ingress Gateway endpoint.  Valtix will receive traffic on this endpoint and proxy the traffic to the appropriate backend application based on the configured policy.  For the Ingress Gateway, this attribute is populated for Gateways deployed in all CSPs (AWS, Azure, GCP, OCI).  For Gateways of `security_type = EGRESS`, this represents the NLB endpoint (IP Address) to be used as a target for routing traffic from the Spoke VPC/VNet/VCN to the Valtix Egress / East-West Gateway.  Valtix will receive traffic from clients, and forward or proxy the traffic to the appropriate destination based on the configured policy.  For the Egress / East-West Gateway, this attribute is only populated for non-AWS Gateways (Azure, GCP, OCI).  For the AWS Gateways, traffic is routed to the AWS Transit Gateway (TGW) or Gateway Load Balancer (GWLB) endpoints.