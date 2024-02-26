# Resource: valtix_spoke_vpc
Resource for creating and managing Spoke VPC protection.

In AWS, the Spoke VPC is attached to the Transit Gateway and appropriate routing is setup in Transit Gateway route tables, so the traffic reaches the Valtix Service VPC. (Customer has to set the default route in the Spoke VPC routing tables to point to the Transit Gateway).

In Azure, VNet peering is setup between the Service VNet and the Spoke VNet.

In GCP, VPC peering is setup between the Service VPC and the Spoke VPC.

## Example Usage

### AWS Spoke VPC (Service VPC and Spoke VPC are in the same AWS Account)
```hcl
resource "valtix_spoke_vpc" "valtix_spoke" {
  service_vpc_id = valtix_service_vpc.service_vpc.id
  spoke_vpc_id   = "vpc-12345678912345678"
}
```

### AWS Spoke VPC (Service VPC and Spoke VPC are in different AWS Accounts)
```hcl
resource "valtix_spoke_vpc" "valtix_spoke" {
  service_vpc_id             = valtix_service_vpc.service_vpc.id
  spoke_vpc_id               = "vpc-12345678912345678"
  spoke_vpc_csp_account_name = "aws-account-2"
  spoke_vpc_region           = "us-west-2"
}
```

### Azure Spoke VNet (Service VNet and Spoke VNet are in the same Azure Subscription)
```hcl
resource "valtix_spoke_vpc" "valtix_spoke" {
  service_vpc_id = valtix_service_vpc.service_vpc.id
  spoke_vpc_id   = "/subscriptions/subid/resourceGroups/rgname/providers/Microsoft.Network/virtualNetworks/spoke1"
}
```

### Azure Spoke VNet (Service VNet and Spoke VNet are in the different Azure Subscriptions)
```hcl
resource "valtix_spoke_vpc" "valtix_spoke" {
  service_vpc_id             = valtix_service_vpc.service_vpc.id
  spoke_vpc_id               = "/subscriptions/subid/resourceGroups/rgname/providers/Microsoft.Network/virtualNetworks/spoke1"
  spoke_vpc_csp_account_name = "azure-account-2"
}
```

### GCP Spoke VPC (Service VPC and Spoke VPC are in the same GCP Project)
```hcl
resource "valtix_spoke_vpc" "valtix_spoke" {
  service_vpc_id = valtix_service_vpc.service_vpc.id
  spoke_vpc_id   = "https://www.googleapis.com/compute/v1/projects/project-004-281801/global/networks/gcp-auto-spoke3"
}
```

### GCP Spoke VPC (Service VPC and Spoke VPC are in different GCP Projects)
```hcl
resource "valtix_spoke_vpc" "valtix_spoke" {
  service_vpc_id             = valtix_service_vpc.service_vpc.id
  spoke_vpc_id               = "https://www.googleapis.com/compute/v1/projects/project-003-281801/global/networks/gcp-auto-spoke6"
  spoke_vpc_csp_account_name = "gcp-account-2"
}
```

## Argument Reference
* `service_vpc_id` - (Required) Service VPC/VNet ID
* `spoke_vpc_id` - (Required) Spoke VPC/VNet ID
* `spoke_vpc_csp_account_name` - (Optional - AWS, Azure, GCP) Valtix CSP Account Name where the Spoke VPC/VNet resides. This argumnent is only required if the Spoke VPC/VNet is in a different Account than the Service VPC/VNet.
* `spoke_vpc_region` - (Optional - AWS) AWS Region where the Spoke VPC resides.  This argument is only required if the Spoke VPC is in a different Region than the Service VPC.
* `transit_gateway_attachment_subnets` - (Optional - AWS) List of Subnet IDs (one per Availability Zone) within the Spoke VPC that will be used to orchestrate the Transit Gateway Attachment ENIs. If not specified, a randomly selected Subnet in each Availability Zone will be used.

## Attribute Reference
* `id` - ID of the Spoke VPC resource

## Import
Spoke VPC resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_spoke_vpc.valtix_spoke 10
```