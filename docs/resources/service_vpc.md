# valtix_service_vpc
Valtix Service VPC/VNet is used for AWS and Azure deployments to create a  Service VPC/VNet as the destination for a Valtix Gateway deployment.  For a Valtix Gateway deployed in AWS using HUB mode, a Service VPC must be deployed as a pre-requisite.  Gateways instances will be deployed in all availability zones associated with the Services VPC.

## Example Usage

### AWS Service VPC
```hcl
resource "valtix_service_vpc" "service_vpc" {
  name                  = "service_vpc"
  csp_account_name      = "aws_account_1"
  region                = "us-east-1"
  cidr                  = "10.0.0.0/16"
  availability_zones    = ["us-east-1a", "us-east-1b"]
  transit_gateway_id    = "tgw-12345678912345678"
}
```

### Azure Service VNet
```
resource "valtix_service_vpc" "service_vpc" {
  name                  = "service_vpc"
  csp_account_name      = "azure_account_1"
  region                = "eastus"
  cidr                  = "10.0.0.0/16"
  availability_zones    = ["1", "2"]
  azure_resource_group  = "resource-group-1"
}
```

## Argument Reference
* `name` - (Required) Name of the Service VPC/VNet
* `csp_account_name` - (Required) The CSP Account name (configured on Valtix) where the Service VPC/VNet will be deployed
* `region` - (Required) The Region/Location where the Service VPC/VNet will be deployed
* `cidr` - (Required) CIDR of the Service VPC/VNet to be deployed
* `availability_zones` - (Required) List of Availability Zones for the Region/Location to associate with the Service VPC/VNet. Valtix Gateways deployed in this Service VPC/VNet will have instances deployed in all associated Availability Zones.
* `transit_gateway_id` - (Required for AWS) Transit Gateway ID for the Service VPC to attach to
* `azure_resource_group` - (Required for Azure) Resource Group Name in which the Service VNet and its resources are created

## Attribute Reference
The following attributes are exported:

* `id` - Valtix Service VPC/VNet ID of the Services VPC/VNet that is created
* `vpc_id` - Cloud specific VPC/VNet ID
* `service_vpc_id` - Same as `id` (for backward compatibility)