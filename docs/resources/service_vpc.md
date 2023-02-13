# Resource: valtix_service_vpc
Resource for creating and managing a Valtix Service VPC/VNet.

A Valtix Service VPC/VNet resource is used in AWS, Azure and GCP deployments to create a Service VPC/VNet as the destination for a Valtix Gateway deployment. For a Valtix Gateway deployed in AWS, Azure or GCP using HUB mode, a Service VPC created and managed by Valtix must be deployed as a pre-requisite. Gateway instances will be deployed in all Availability Zones associated with the Service VPC.

## Example Usage

### AWS Service VPC
```hcl
resource "valtix_service_vpc" "aws_service_vpc" {
  name               = "aws-service-vpc"
  csp_account_name   = "aws-account1"
  region             = "us-east-1"
  cidr               = "10.0.0.0/23"
  availability_zones = ["us-east-1a", "us-east-1b"]
  transit_gateway_id = "tgw-12345678912345678"
  use_nat_gateway    = true
}
```
~> **Note on AWS Transit Gateway (TGW)**
If the TGW is created using the AWS Terraform Provider, the attributes *default_route_table_association* and *default_route_table_propagation* MUST be set to *"disable"*<br>
If the TGW is created using the AWS Console, the attributes *Default association route table* and *Default propagation route table* MUST be set to *Disable*
If these values are not set properly, traffic will bypass the Service VPC and will not be protected by Valtix.

### Azure Service VNet
```hcl
resource "valtix_service_vpc" "azure_service_vnet" {
  name                 = "azure-service-vnet"
  csp_account_name     = "azure-account1"
  region               = "eastus"
  cidr                 = "10.0.0.0/23"
  availability_zones   = ["1", "2"]
  azure_resource_group = "resource-group-1"
}
```

### GCP Service VPC
```hcl
resource "valtix_service_vpc" "gcp_service_vpc" {
  name               = "gcp-service-vpc"
  csp_account_name   = "gcp-account1"
  region             = "us-east1"
  cidr               = "10.0.0.0/24"
  management_cidr    = "10.0.1.0/24"
  availability_zones = ["us-east1-b", "us-east1-c"]
}
```

## Argument Reference
* `name` - (Required) Name of the Service VPC/VNet
* `csp_account_name` - (Required) The CSP Account name (configured in Valtix) where the Service VPC/VNet will be deployed
* `region` - (Required) The Region/Location where the Service VPC/VNet will be deployed
* `cidr` - (Required) CIDR of the Service VPC/VNet to be deployed.  For GCP only: This CIDR is used for the Datapath Subnet created within the Datapath VPC as part of the GCP Service VPC construct. When the Gateways are deployed within the GCP Service VPC construct (Management and Datapath VPCs), the datapath interfaces will be deployed in the Datapath VPC / Subnet.  This CIDR block must be different than the CIDR specified in the `management_cidr` argument.
* `availability_zones` - (Required) List of Availability Zones for the Region/Location to associate with the Service VPC/VNet. Valtix Gateways deployed in this Service VPC/VNet will have instances deployed in all associated Availability Zones.
* `transit_gateway_id` - (Required for AWS) Transit Gateway ID for the Service VPC to attach to
* `azure_resource_group` - (Required for Azure) Resource Group Name in which the Service VNet and its resources are created
* `management_cidr` - (Required for GCP) The CIDR used for the Management Subnet created within the Management VPC as part of the GCP Service VPC construct. When the Gateways are deployed within the GCP Service VPC construct (Management and Datapath VPCs), the management interfaces will be deployed in the Management VPC / Subnet.  This CIDR block must be different than the CIDR specified in the `cidr` argument.
* `use_nat_gateway` - (Optional for AWS) `true` or `false`. If `true`, enables egress communication through NAT gateway in each AZ (Default `false`).

## Attribute Reference
* `id` - ID of the Services VPC/VNet resource that can be referenced in other resources (e.g., *valtix_gateway*)
* `vpc_id` - Cloud specific ID of the Services VPC/VNet
* `service_vpc_id` - (Deprecated) Same as the `id` attribute

## Import
[*Public Preview*] Service VPC/VNet resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_service_vpc.aws_service_vpc 10
```