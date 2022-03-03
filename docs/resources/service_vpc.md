# valtix_service_vpc
Resource for creating and managing the Valtix Service VPC/VNet.  A Valtix Service VPC/VNet resource is used in AWS and Azure deployments to create a Service VPC/VNet as the destination for a Valtix Gateway deployment.  For a Valtix Gateway deployed in AWS or Azure using HUB mode, a Service VPC must be deployed as a pre-requisite.  Gateway instances will be deployed in all Availability Zones associated with the Services VPC.

## Example Usage

### AWS Service VPC
```hcl
resource "valtix_service_vpc" "service_vpc" {
  name               = "service_vpc"
  csp_account_name   = "aws_account_1"
  region             = "us-east-1"
  cidr               = "10.0.0.0/16"
  availability_zones = ["us-east-1a", "us-east-1b"]
  transit_gateway_id = "tgw-12345678912345678"
  use_nat_gateway    = "true"
}
```
~> **Note on AWS Transit Gateway (TGW)**
If the TGW is created using the AWS Terraform Provider, the attributes *default_route_table_association* and *default_route_table_propagation* MUST be set to *"disable"*<br>
If the TGW is created using the AWS Console, the attributes *Default association route table* and *Default propagation route table* MUST be set to *Disable*
If these values are not set properly, traffic will bypass the Service VPC and will not be protected by Valtix.

### Azure Service VNet
```hcl
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
* `name` - (Required) Name of the Service VPC/VNet
* `csp_account_name` - (Required) The CSP Account name (configured in Valtix) where the Service VPC/VNet will be deployed
* `region` - (Required) The Region/Location where the Service VPC/VNet will be deployed
* `cidr` - (Required) CIDR of the Service VPC/VNet to be deployed
* `availability_zones` - (Required) List of Availability Zones for the Region/Location to associate with the Service VPC/VNet. Valtix Gateways deployed in this Service VPC/VNet will have instances deployed in all associated Availability Zones.
* `transit_gateway_id` - (Required for AWS) Transit Gateway ID for the Service VPC to attach to
* `azure_resource_group` - (Required for Azure) Resource Group Name in which the Service VNet and its resources are created
* `use_nat_gateway` - (Optional for AWS) `true` or `false`. If `true`, enables egress communication through NAT gateway in each AZ (Default `false`).

## Attribute Reference
* `id` - Terraform resource ID of the Services VPC/VNet
* `vpc_id` - Cloud specific ID of the Services VPC/VNet
* `service_vpc_id` - Same as `id` (for backward compatibility)