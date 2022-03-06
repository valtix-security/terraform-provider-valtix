# Resource: valtix_spoke_vpc
Resource for creating and managing Spoke VPC protection.

In AWS, the Spoke VPC is attached to the Transit Gateway and appropriate routing is setup in Transit Gateway route tables, so the traffic reaches the Valtix Service VPC. (Customer has to set the default route in the Spoke VPC routing tables to point to the Transit Gateway).

In Azure, VNet peering is setup between the Service VNet and the Spoke VNet

## Example Usage

### AWS Spoke VPC (Service VPC and Spoke VPC is the same AWS Account)
```hcl
resource "valtix_spoke_vpc" "valtix_spoke" {
  service_vpc_id = valtix_service_vpc.service_vpc1.id
  spoke_vpc_id   = "vpc-12345678912345678"
}
```

### AWS Spoke VPC (Service VPC and Spoke VPC are in different AWS Accounts)
```hcl
resource "valtix_spoke_vpc" "valtix_spoke" {
  service_vpc_id             = valtix_service_vpc.service_vpc1.id
  spoke_vpc_id               = "vpc-12345678912345678"
  spoke_vpc_csp_account_name = "valtix-csp-account2-name"
  spoke_vpc_region           = "us-west-2"
}
```

### Azure Spoke VNet
```
resource "valtix_spoke_vpc" "valtix_spoke" {
  service_vpc_id = valtix_service_vpc.service_vpc1.id
  spoke_vpc_id   = "/subscriptions/subid/resourceGroups/rgname/providers/Microsoft.Network/virtualNetworks/spoke1"
}
```

## Argument Reference

* `service_vpc_id` - (Required) Service VPC/VNet ID
* `spoke_vpc_id` - (Required) Spoke VPC/VNet ID
* `spoke_vpc_csp_account_name` - (Optional - AWS) CSP Account Name (added on Valtix) where the Spoke VPC exists (if different from the csp account name in which Service VPC is created)
* `spoke_vpc_region` - (Optional - AWS) Region where the spoke vpc exists (if different from the CSP account name in which Service VPC is created)