# valtix_service_vpc

Valtix Service VPC is used for AWS deployments to create a Valtix Service VPC as the destination for a Valtix Gateway deployment.  Please note that for AWS HUB mode deployment that Valtix Service VPC must be deployed as a pre-requisite and the HUB mode gateway will deploy in all availability zones defined in the availability_zones argument

## Example Usage

### AWS Service VPC
```hcl
resource "valtix_service_vpc" service_vpc {
  name               = "service_vpc"
  csp_account_name   = "aws_account_1"
  region             = "us-east-1"
  cidr               = "10.0.0.0/16"
  availability_zones = ["us-east-1a", "us-east-1b"]
  transit_gateway_id = "tgw-12345678912345678"
}
```

### Azure Service VNet
```
resource "valtix_service_vpc" "service_vpc" {
  name                 = "service_vpc"
  csp_account_name     = "azure_account_1"
  region               = "eastus"
  cidr                 = "10.0.0.0/16"
  availability_zones   = ["1", "2"]
  azure_resource_group = "resource-group-1"
}
```

## Argument Reference

* `name` - (Required) Name of the service VPC
* `csp_account_name` - (Required) The CSP account name (configured on Valtix) where the service VPC/VNet will be deployed
* `region` - (Required) The region/location where the service VPC will be deployed
* `cidr` - (Required) The CIDR of the service VPC/VNet to be deployed
* `availability_zones` - (Required) The list of availability zones that the service VPC/VNet subnets are created. Valtix Gateways deployed in this service VPC/VNet will use all availability zones defined here
* `transit_gateway_id` - (Required for AWS) Transit Gateway ID for the service VPC to attach to
* `azure_resource_group` - (Required for Azure) Resource Group Name in which the service VNet and its resources are created

## Attribute Reference

The following attributes are exported:

* `id` - Valtix Service VPC/VNet ID of the Services VPC/VNet that is created
* `vpc_id` - Cloud specific VPC/VNet Id
* `service_vpc_id` - Same as `id` (for backward compatibility)
