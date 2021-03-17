# DataSource: valtix_policy_rule_set

A policy rule set is a list of firewall rules. Use this data-source to get the ID of the Policy Rule Set. The rule set can be applied to multiple gateways to achieve identical security posture.

## Example Usage

```hcl
data "valtix_policy_rule_set" ingress_policy_rule_set {
  name = "ingress_rule_set"
}

resource "valtix_gateway" aws-gw1 {
  name                    = "aws-gw1"
  description             = "AWS Gateway 1"
  csp_account_name        = "aws_account_1"
  instance_type           = "AWS_M5_2XLARGE"
  gateway_image           = "2.3-04"
  mode                    = "EDGE"
  security_type           = "INGRESS"
  policy_rule_set_id      = data.valtix_policy_rule_set.ingress_policy_rule_set.rule_set_id
  ssh_key_pair            = "ssh_keypair1"
  aws_iam_role_firewall   = "iam_role_name_for_firewall"
  region                  = "us-east-1"
  vpc_id                  = data.aws_vpc.gw_vpc.id
  mgmt_security_group     = data.aws_security_group.gw_mgmt_sg.id
  datapath_security_group = data.aws_security_group.gw_datapath_sg.id
  instance_details {
    availability_zone = var.aws_zone1_name
    mgmt_subnet       = data.aws_subnet.gw_mgmt_subnet1.id
    datapath_subnet   = data.aws_subnet.gw_datapath_subnet1.id
  }
}


```

## Argument Reference

* `name` - (Required) Name of the rule set

## Attributes Reference

* `rule_set_id` - Set to the RuleSetID of the found resource
