# valtix_service_vpc

Valtix Service VPC is used for AWS deployments to create a Valtix Service VPC as the destination for a Valtix Gateway deployment.  Please note that for AWS HUB mode deployment that Valtix Service VPC must be deployed as a pre-requisite and the HUB mode gateway will deploy in all availability zones defined in the availability_zones argument

## Example Usage

```hcl
resource "valtix_service_vpc" service_vpc {
  name               = "service_vpc"
  csp_account_name   = "aws_account_1"
  region             = "us-east-1"
  cidr               = "10.10.0.0/16"
  availability_zones = ["us-east-1a", "us-east-1b"]
}
```

## Argument Reference

* `name` - (Required) Name of the service VPC
* `csp_account_name` - (Required) The CSP account where the service VPC will be deployed
* `region` - (Required) The AWS region where the service VPC will be deployed
* `cidr` - (Required) The CIDR of the service VPC to be deployed
* `availability_zones` - (Required) The list of availability zones that the service VPC will use.  HUB mode gateways deployed in this service VPC will use all availability zones defined here

## Attribute Reference

The following attributes are exported:

* `id` - (Required) VPC ID of the Services VPC that is created