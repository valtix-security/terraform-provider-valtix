# DataSource: valtix_ftdv_gateway
Data source for obtaining attributes of a Firewall Threat Defense Virtual (FTDv) Gateway resource

## Example Usage
```hcl
data "valtix_ftdv_gateway" "aws_ftdv_gw1" {
  name = "aws-ftdv-gw1"
}
```

## Argument Reference
* `name` - (Required) Name of the Gateway resource

## Attributes Reference
* `id` - ID of the Gateway resource
* `ftdv_version` - Image version of the Gateway
* `ftdv_license_model` - Licensing models applied to the Gateway
* `ftdv_performance_tier` - Performance tier applied to the Gateway 
* `ftdv_policy_id` - ID of the policy applied to the Gateway
* `gateway_endpoint` - (Azure only) The NLB endpoint (IP Address) to be used as a target for routing traffic to the Gateway
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