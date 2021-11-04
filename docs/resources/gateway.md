# Resource: valtix_gateway
Create a valtix gateway  

Please note that with the exclusion of description and gateway_image, any other argument changes are prevented at this time as it would result in a service interruption to the Valtix gateway. In order to change these arguments, you must destroy and then create the valtix_gateway resource.

[valtix_cloud_account](../valtix_cloud_account), [valtix_policy_rule_set](../valtix_policy_rule_set)
must be defined before valtix_gateway can be created

## Example Usage

### AWS Gateway (Edge Mode, for INGRESS only)
```hcl
resource "valtix_gateway" "aws-gw1" {
  name                    = "aws-gw1"
  description             = "AWS Gateway 1"
  csp_account_name        = valtix_cloud_account.aws_act.name
  instance_type           = "AWS_M5_2XLARGE"
  gateway_image           = "2.3-04"
  mode                    = "EDGE"
  security_type           = "INGRESS"
  policy_rule_set_id      = valtix_policy_rule_set.ingress_policy_rule_set.rule_set_id
  ssh_key_pair            = "ssh_keypair1"
  aws_iam_role_firewall   = "iam_role_name_for_firewall"
  region                  = "us-east-1"
  vpc_id                  = "vpc-123456789"
  mgmt_security_group     = "sg-123456788"
  datapath_security_group = "sg-123456789"
  instance_details {
    availability_zone = "us-east-1a"
    mgmt_subnet       = "subnet-123456786"
    datapath_subnet   = "subnet-123456787"
  }
  instance_details {
    availability_zone = "us-east-1b"
    mgmt_subnet       = "subnet-123456788"
    datapath_subnet   = "subnet-123456789"
  }
}
```

### AWS HUB mode (in a Service VPC and using Transit Gateway)
```hcl
resource "valtix_gateway" "aws-hub-gw1" {
  name                  = "aws-hub-gw1"
  description           = "AWS Gateway 1"
  csp_account_name      = valtix_cloud_account.aws_act.name
  instance_type         = "AWS_M5_2XLARGE"
  gateway_image         = "2.3.04"
  mode                  = "HUB"
  security_type         = "EGRESS"
  policy_rule_set_id    = valtix_policy_rule_set.egress_policy_rule_set.rule_set_id
  ssh_key_pair          = "ssh_keypair1"
  aws_iam_role_firewall = "iam_role_name_for_firewall"
  region                = "us-east-1"
  vpc_id                = valtix_service_vpc.service_vpc.id
  aws_gateway_lb        = "true"
}
```

For HUB mode INGRESS gateway set the **security_type = INGRESS** and remove "**aws_gateway_lb**" argument

### Azure Gateway (EDGE Mode)
```hcl
resource "valtix_gateway" azure_gw1 {
  name                    = "gw1"
  csp_account_name        = valtix_cloud_account.azure_act.name
  instance_type           = "AZURE_F8S_V2"
  azure_resource_group    = "rg1"
  gateway_image           = var.gateway_image
  mode                    = "EDGE"
  security_type           = "INGRESS"
  ssh_public_key          = file(var.ssh_public_key_file)
  azure_user_name         = "centos"
  policy_rule_set_id      = valtix_policy_rule_set.egress_policy_rule_set.rule_set_id
  region                  = var.region
  vpc_id                  = "/subscriptions/12345/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnet1"
  mgmt_security_group     = "/subscriptions/12345/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/mgmt-sg1"
  datapath_security_group = "/subscriptions/12345/resourceGroups/rg1/providers/Microsoft.Network/networkSecurityGroups/dp-sg1"
  instance_details {
    availability_zone = "1"
    mgmt_subnet       = "/subscriptions/12345/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnet1/subnets/mgmt1"
    datapath_subnet   = "/subscriptions/12345/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnet1/subnets/dp1"
  }
  instance_details {
    availability_zone = "2"
    mgmt_subnet       = "/subscriptions/12345/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnet1/subnets/mgmt2"
    datapath_subnet   = "/subscriptions/12345/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnet1/subnets/dp2"
  }
}
```

### Azure Gateway (HUB Mode - Service VNet Managed by Valtix)
```hcl
resource "valtix_gateway" "azure_gw1" {
  name                 = "gw1"
  csp_account_name     = valtix_cloud_account.azure_act.name
  instance_type        = "AZURE_F8S_V2"
  azure_resource_group = "rg1"
  gateway_image        = var.gateway_image
  mode                 = "HUB"
  security_type        = "INGRESS"
  ssh_public_key       = file(var.ssh_public_key_file)
  azure_user_name      = "centos"
  policy_rule_set_id   = valtix_policy_rule_set.egress_policy_rule_set.rule_set_id
  region               = var.region
  vpc_id               = valtix_service_vpc.svpc1.vpc_id
}
```

For EGRESS and East-West gateway set the **security_type = EGRESS**

### GCP Gateway
```hcl
resource "valtix_gateway" "gcp-gw" {
  name                      = "gcp-gw"
  description               = "GCP gateway"
  csp_account_name          = valtix_cloud_account.gcp_act.name
  instance_type             = "GCP_E2_8"
  gateway_image             = "2.3-01"
  mode                      = "EDGE"
  security_type             = "INGRESS"
  policy_rule_set_id        = valtix_policy_rule_set.ingress_policy_rule_set.rule_set_id
  gcp_service_account_email = "valtix-controller@gcp-project.iam.gserviceaccount.com"
  region                    = "us-west1"
  vpc_id                    = "https://www.googleapis.com/compute/v1/projects/gcp-project/global/networks/datapath-vpc"
  mgmt_vpc_id               = "https://www.googleapis.com/compute/v1/projects/gcp-project/global/networks/mgmt-vpc"
  # vpc_id                  = data.google_compute_network.datapath_vpc.self_link
  # mgmt_vpc_id             = data.google_compute_network.mgmt_vpc.self_link
  mgmt_security_group       = "valtix-mgmt"
  datapath_security_group   = "valtix-datapath"
  log_profile               = valtix_profile_log_forwarding.splunk1.profile_id

  instance_details {
    availability_zone = "us-west1-a"
    mgmt_subnet       = "https://www.googleapis.com/compute/v1/projects/gcp-project/regions/us-west1/subnetworks/mgmt-subnet"
    datapath_subnet   = "https://www.googleapis.com/compute/v1/projects/gcp-project/regions/us-west1/subnetworks/datapath-subnet"
    # mgmt_subnet     = data.google_compute_subnetwork.mgmt_subnet1.self_link
    # datapath_subnet = data.google_compute_subnetwork.datapath_subnet1.self_link
  }

  instance_details {
    availability_zone = "us-west1-b"
    mgmt_subnet       = "https://www.googleapis.com/compute/v1/projects/gcp-project/regions/us-west1/subnetworks/mgmt-subnet"
    datapath_subnet   = "https://www.googleapis.com/compute/v1/projects/gcp-project/regions/us-west1/subnetworks/datapath-subnet"
  }
}
```

## Argument Reference

* `name` - (Required) Name of the Valtix gateway.
* `description` - (Optional) Description of the Valtix gateway
* `csp_account_name` - (Required) The CSP account where the gateway will be deployed.
* `instance_type` - (Required) Must be one of:
    * **GCP_E2_8**
    * **AWS_M5_2XLARGE**
    * **AZURE_D8S_V3**
* `gateway_image` - (Required) Example "2.3-01". This is the Valtix image version to be deployed for this gateway. Please consult with Valtix support for recommended version.
* `mode` - (Required) "EDGE" or "HUB". Look into product documentation for different deployment modes
* `security_type` - (Optional) "INGRESS" or "EGRESS". Default "INGRESS"
* `policy_rule_set_id` - (Required) Rule set id of valtix_policy_rule_set. *(e.g. valtix_policy_rule_set.ruleset1.rule_set_id)*
* `ssh_key_pair` - (AWS - Required) SSH key pair name that's already in your AWS account
* `ssh_public_key` - (Azure - Required) Contents of SSH public key
* `gcp_service_account_email` - (GCP - Required) This is the GCP gateway service account email, that provides permissions for the Valtix gateway to integrate with other GCP project resources such as Secrets Manager and storage buckets. 
* `aws_iam_role_firewall` - (AWS - Required) This is the IAM role that's assigned to the Valtix firewall instances.
* `azure_user_identity_id` - (Azure - Optional) User assgined identity This is the IAM role that's assigned to the Valtix firewall instances.
* `azure_resource_group` - (Azure - Required) Azure resource group name used for all Valtix gateway resources
* `region` - (Required) Region where the Valtix gateway is deployed.
* `vpc_id` - (Required) VPC ID where the Valtix gateway is deployed and is used for data traffic to be inspected. This must be either the VPC where you apps run or the shared services VPC that's peered (or hub via transit gateway) to other spoke (app) VPCs.  Please note that for HUB mode, this vpc_id must refer to **id** attribute that is exported using the [valtix_service_vpc](/terraform/valtix_service_vpc/#valtix_service_vpc) resource
* `aws_gateway_lb` - (Optional only for AWS HUB mode) "true" or "false". If attribute is "true", this will deploy AWS Gateway using AWS Gateway Load Balancer. This is only for EGRESS gateway that will support both East-West and Egress traffic.
* `mgmt_vpc_id` - (GCP - Required) GCP VPC ID where the management interface of the Valtix Gateway is attached.
* `mgmt_security_group` - (Required except for AWS HUB mode) Security group ID for management traffic or GCP network tag to be used to define GCP firewall rules for Valtix firewall instances to communicate with the Valtix controller. This must allow all outbound access from Valtix management interface
* `datapath_security_group` - (Required except for AWS HUB mode) Security group ID for the datapath traffic (application traffic) or GCP network tag to be used to define GCP firewall rules for application traffic to pass through the Valtix Gateway. This must allow traffic to the ports that are defined as services on the Valtix controller
* `min_instances` - (Optional) minimum number of instances per zone (default 1)
* `max_instances` - (Optional) maximum number of instances per zone (default 1)
* `health_check_port` - (Optional) port number that NLB uses to monitor the instances (default 65534). A rule must be configured on the datapath_security_group to allow traffic to this port.
* `log_profile` - (Optional) log profile id *(e.g. valtix_profile_log_forwarding.splunk1.profile_id)*
* `packet_capture_profile` - (Optional) packet profile id *(e.g. valtix_profile_packet_capture.pcap1.profile_id)*
* `diagnostics_profile` - (Optional) diagnostics profile id *(e.g. valtix_profile_diagnostics.diag1.profile_id)*
* `settings` - (Optional) Gateway settings. This block can be repeated multiple times. Please check [this section](#gateway-settings) for the structure. 
* `instance_details` - (Required) This block can be repeated multiple times for deploying gateway instances in multiple zones. Look below for the [structure](#instance-details) of this block. Atleast 1 block must be provided.

## Instance Details
This section is not required for AWS/Azure HUB mode as instance details are obtained from service VPC referenced in vpc_id attribute

* `availability_zone` - (Required) specifies the availability zone where the Valtix gateway instance(s) are deployed
* `mgmt_subnet` - (Required) specifies the VPC subnet ID used for management traffic where the Valtix gateway instance(s) are deployed for this availability zone.
* `datapath_subnet` - (Required) specifies the VPC subnet ID used for data traffic where the Valtix gateway instance(s) are deployed for this availability zone.

## Gateway settings
Gateway settings define a list of settings that applies to the given gateway

### To enable EBS encryption for the gateway instances using default KMS key
```hcl
settings {
  name  = "gateway.aws.ebs.encryption.key.default"
  value = "true"
}
```

### To enable EBS encryption for the gateway instances using specified KMS key
```hcl
settings {
  name  = "gateway.aws.ebs.encryption.key.customer_key"
  value = "<KMS key ID>"
}
```

### To add a list of custom tags to the gateway instances
```hcl
settings {
  name  = "custom_tags"
  value = "[{\"key\":\"customer_key1\",\"value\":\"customer_value1\"},{\"key\":\"customer_key2\",\"value\":\"customer_value2\"}]"
}
```

## Attribute Reference
* `gateway_gwlb_endpoints` - AWS Gateway Load Balancer endpoints created in each of the AZs. It's in the following format

    ```hcl
    gateway_gwlb_endpoints {
        endpoint_id          = "vpce-047c749fc6f7e0c0d"
        network_interface_id = "eni-017eacdb23d2ebaf4"
        subnet_id            = "subnet-0d61750e97caafd9d"
    }
    gateway_gwlb_endpoints {
        endpoint_id          = "vpce-0707fa3f03c5064a7"
        network_interface_id = "eni-020464bd838461bca"
        subnet_id            = "subnet-0fd61e07f200224f1"
    }
    ```

* `gateway_endpoint` - For the Ingress Gateway, shows the NLB DNS/IP of the Valtix Gateway. This must be used as an endpoint for your application and Valtix proxies the traffic received on this endpoint to the target application configured