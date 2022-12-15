# DataSource: valtix_service_vpc
Data source for obtaining the attributes of a Service VPC/VNet resource.  A Service VPC/VNet resource is in a centralized Gateway resource deployment.

## Example Usage
```hcl
data "valtix_service_vpc" "aws_service_vpc" {
  csp_account_name  = "aws_account1"
  region            = "us-east-1"
  vpc_id            = "vpc-0669e3afda800ca03"
}

resource "valtix_gateway" "aws-hub-gw1" {
  name                   = "aws-hub-gw1"
  description            = "AWS Gateway 1"
  csp_account_name       = valtix_cloud_account.aws_act.name
  instance_type          = "AWS_M5_2XLARGE"
  gateway_image          = var.gateway_image
  gateway_state          = "ACTIVE"
  mode                   = "HUB"
  security_type          = "EGRESS"
  policy_rule_set_id     = valtix_policy_rule_set.egress_policy_rule_set.id
  ssh_key_pair           = "ssh_keypair1"
  aws_iam_role_firewall  = "iam_role_name_for_firewall"
  region                 = "us-east-1"
  vpc_id                 = data.valtix_service_vpc.service_vpc.id
  log_profile            = valtix_profile_log_forwarding.datadog.id
  packet_capture_profile = valtix_profile_packet_capture.awspcap1.id
  aws_gateway_lb         = true
}
```

## Argument Reference
* `csp_account_name` - (Required) Name of the onboarded CSP Account where the Service VPC/VNet resource exists
* `region` - (Required) Region where the Service VPC/VNet resource exists
* `vpc_id` - (Required) VPC/VNet ID of the Service VPC/VNet resource

## Attributes Reference
* `id` - ID of the Service VPC/VNet resource
* `name` - Name of the Service VPC/VNet resource