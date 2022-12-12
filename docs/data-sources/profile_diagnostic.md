# DataSource: valtix_diagnostic_profile
Data source for obtaining attributes of a Diagnostic Profile resource.  The attributes can be used in the arguments of a Gateway resource.

## Example Usage
```hcl
data "valtix_profile_diagnostic" "awsdiag1" {
  name = "awsdiag1"
}

resource "valtix_gateway" "aws-hub-gw1" {
  name                  = "aws-hub-gw1"
  description           = "AWS Gateway 1"
  csp_account_name      = valtix_cloud_account.aws_act.name
  instance_type         = "AWS_M5_2XLARGE"
  gateway_image         = var.gateway_image
  gateway_state         = "ACTIVE"
  mode                  = "HUB"
  security_type         = "EGRESS"
  policy_rule_set_id    = valtix_policy_rule_set.egress_policy_rule_set.id
  ssh_key_pair          = "ssh_keypair1"
  aws_iam_role_firewall = "iam_role_name_for_firewall"
  region                = "us-east-1"
  vpc_id                = valtix_service_vpc.service_vpc.id
  diagnostics_profile   = data.valtix_profile_diagnostic.awsdiag1.id
  aws_gateway_lb        = true
}
```

## Argument Reference
* `name` - (Required) Name of the Diagnostic Profile resource

## Attributes Reference
* `id` - ID of the Diagnostic Profile resource