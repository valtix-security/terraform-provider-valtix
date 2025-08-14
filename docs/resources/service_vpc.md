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
* `csp_account_name` - (Required) The CSP Account name (configured in Multicloud Defense) where the Service VPC/VNet will be deployed
* `region` - (Required) The Region/Location where the Service VPC/VNet will be deployed
* `cidr` - (Required) CIDR of the Service VPC/VNet to be deployed.  For GCP only: This CIDR is used for the Datapath Subnet created within the Datapath VPC as part of the GCP Service VPC construct. When the Gateways are deployed within the GCP Service VPC construct (Management and Datapath VPCs), the datapath interfaces will be deployed in the Datapath VPC / Subnet.  This CIDR block must be different than the CIDR specified in the `management_cidr` argument.
* `availability_zones` - (Required) List of Availability Zones for the Region/Location to associate with the Service VPC/VNet. Multicloud Defense Gateways deployed in this Service VPC/VNet will have instances deployed in all associated Availability Zones.
* `transit_gateway_id` - (Required for AWS) Transit Gateway ID for the Service VPC to attach to
* `azure_resource_group` - (Required for Azure) Resource Group where the Service VNet and its resources will be orchestrated
* `management_cidr` - (Required for GCP) The CIDR used for the Management Subnet created within the Management VPC as part of the GCP Service VPC construct. When the Gateways are deployed within the GCP Service VPC construct (Management and Datapath VPCs), the management interfaces will be deployed in the Management VPC / Subnet.  This CIDR block must be different than the CIDR specified in the `cidr` argument.
* `use_nat_gateway` - (Optional for AWS) Applicable values are `true` or `false`. If set to `true`, AWS NAT Gateways will be orchestrated to use for all egress traffic to the Internet.  If not specified, the default value is `false`.
* `azure_vwan_details` - (Optional for Azure) Specifies the Azure vWAN integration to peer the Service vNet to an existing vWAN vHub and orchestrate the spoke vNet CIDRs as static routes into the vWAN vHub route tables. See [Azure vWAN Details](#azure-vwan-details) for the block structure.

## Azure vWAN Details
Azure vWAN integration to peer the Service vNet to an existing vWAN vHub and orchestrate the spoke vNet CIDRs as static routes into the vWAN vHub route tables.

### To add a map of user-specified Tags to the Gateway instances
```hcl
azure_vwan_details {
  vhub_name = "vwan-vhub-us-west-2"
  vhub_resource_group = "vwan-vhub-rg"
  vhub_association_route_table_id = "/subscriptions/0a1b234c-0a1b-012a-0abc-01234567a8b9/resourceGroups/vwan-vhub-rg/providers/Microsoft.Network/virtualHubs/vwan-vhub-us-west-2/hubRouteTables/defaultRouteTable"
  vhub_propagation_route_table_ids = ["/subscriptions/0a1b234c-0a1b-012a-0abc-01234567a8b9/resourceGroups/vwan-vhub-rg/providers/Microsoft.Network/virtualHubs/vwan-vhub-us-west-2/hubRouteTables/defaultRouteTable"]
  spokevpc_cidrs = ["10.0.1.0/24","10.0.2.0/24","10.0.3.0/24"]
  propagate_cidrs = true
}
```

### Azure vWAN Details Argument Reference
* `vhub_name` - (Required) Name of the Azure vWAN vHub
* `vhub_resource_group` - (Required) Resource Group where the vWAN vHub resides
* `vhub_association_route_table_id` - (Required) The full path of the route table resource used to associate with the peering
* `vhub_propagation_route_table_ids` - (Required) The list of full paths of vWAN spoke vNet peering route table resources into which the CIDRs of the Service vNet spoke vNets will be propogated
* `propagate_cidrs` - (Optional) Whether to propogate the CIDRs of the Service vNet spoke vNet peers into the list of route tables defined in the `vhub_propagation_route_table_ids` arguement. Applicable values are `true` or `false`. If the value specified is `true`, all orchestrated spoke vNet peered CIDRs will be propagated. If the value specified is `false`, only the CIDRs listed in the `spokevpc_cidrs` argument will be propagated. If not specified, the default value is `true`.  
* `spokevpc_cidrs` - (Optional) List of Service vNet spoke vNet CIDRs to propogate into the list of route tables defined in the `vhub_propagation_route_table_ids` argument. This is only applicable if the `propagate_cidrs` argument is specified as `false`. If not specified and the `propagate_cidrs` argument is specified as `false`, then this will be treated as an empty list and no CIDRs will be propagated.

~> **Note on Azure vWAN Details**
Only a single block can be specified per Service vNet resource as the Service vNet can only be integrated with a single Azure vWAN vHub.

## Attribute Reference
* `id` - ID of the Services VPC/VNet resource that can be referenced in other resources (e.g., *valtix_gateway*)
* `vpc_id` - Cloud specific ID of the Services VPC/VNet

## Import
Service VPC/VNet resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_service_vpc.aws_service_vpc 10
```