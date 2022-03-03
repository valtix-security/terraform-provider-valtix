# DataSource: valtix_service_vpc
Data source for obtaining the ID of a Valtix Service VPC/VNet.  A Valtix Service VPC/VNet resource is used in AWS and Azure deployments to create a Service VPC/VNet as the destination for a Valtix Gateway deployment.  For a Valtix Gateway deployed in AWS or Azure using HUB mode, a Service VPC must be deployed as a pre-requisite.  Gateway instances will be deployed in all Availability Zones associated with the Services VPC.

## Example Usage
```hcl
data "valtix_service_vpc" "service_vpc" {
  csp_account_name   = "aws_account_1"
  region             = "us-east-1"
  vpc_id             = "vpc-0669e3afda800ca03"
}

resource "valtix_gateway" "aws-hub-gw1" {
  name                  = "aws-hub-gw1"
  description           = "AWS Gateway 1"
  csp_account_name      = valtix_cloud_account.aws_act.name
  instance_type         = "AWS_M5_2XLARGE"
  gateway_image         = "2.11-05"
  gateway_state         = "ACTIVE"
  mode                  = "HUB"
  security_type         = "EGRESS"
  policy_rule_set_id    = valtix_policy_rule_set.egress_policy_rule_set.rule_set_id
  ssh_key_pair          = "ssh_keypair1"
  aws_iam_role_firewall = "iam_role_name_for_firewall"
  region                = "us-east-1"
  vpc_id                = data.valtix_service_vpc.service_vpc.id
  aws_gateway_lb        = "true"
}
```

## Argument Reference
* `csp_account_name` - (Required) Valtix friendly name for the on-boarded CSP Account where the Service VPC/VNet resource exists
* `region` - (Required) CSP Region where the Service VPC/VNet resource exists
* `vpc_id` - (Required) The VPC/VNet ID of the Service VPC/VNet resource

## Attributes Reference
* `id` - Set to the Terraform Service VPC ID of the found resource