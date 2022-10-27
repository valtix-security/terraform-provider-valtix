# Resource: valtix_gateway
Resource for creating and managing Valtix Gateways

~> **Note on valtix_gateway resource creation**
The [`valtix_cloud_account`](../valtix_cloud_account) and [`valtix_policy_rule_set`](../valtix_policy_rule_set)
resources must be created before the `valtix_gateway` resource can be created

## Example Usage

### AWS Gateway (EDGE Mode, for INGRESS only)
```hcl
resource "valtix_gateway" "aws-gw1" {
  name                    = "aws-gw1"
  description             = "AWS Gateway 1"
  csp_account_name        = valtix_cloud_account.aws_act.name
  instance_type           = "AWS_M5_2XLARGE"
  gateway_image           = "22.06-04"
  gateway_state           = "ACTIVE"
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

### AWS (HUB Mode - Service VPC Managed by Valtix)
```hcl
resource "valtix_gateway" "aws-hub-gw1" {
  name                   = "aws-hub-gw1"
  description            = "AWS Gateway 1"
  csp_account_name       = valtix_cloud_account.aws_act.name
  instance_type          = "AWS_M5_2XLARGE"
  gateway_image          = var.gateway_image
  gateway_state          = "ACTIVE"
  mode                   = "HUB"
  security_type          = "EGRESS"
  policy_rule_set_id     = valtix_policy_rule_set.egress_policy_rule_set.rule_set_id
  ssh_key_pair           = "ssh_keypair1"
  aws_iam_role_firewall  = "iam_role_name_for_firewall"
  region                 = "us-east-1"
  vpc_id                 = valtix_service_vpc.service_vpc.id
  aws_gateway_lb         = true
}
```

For HUB mode INGRESS Gateway set the `security_type = INGRESS` and remove `aws_gateway_lb` argument

### Azure Gateway (EDGE Mode)
```hcl
resource "valtix_gateway" azure_gw1 {
  name                    = "gw1"
  csp_account_name        = valtix_cloud_account.azure_act.name
  instance_type           = "AZURE_F8S_V2"
  azure_resource_group    = "rg1"
  gateway_image           = "22.06-04"
  gateway_state           = "ACTIVE"
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
  name                    = "gw1"
  csp_account_name        = valtix_cloud_account.azure_act.name
  instance_type           = "AZURE_F8S_V2"
  azure_resource_group    = "rg1"
  gateway_image           = var.gateway_image
  gateway_state           = "ACTIVE"
  mode                    = "HUB"
  security_type           = "INGRESS"
  ssh_public_key          = file(var.ssh_public_key_file)
  azure_user_name         = "centos"
  policy_rule_set_id      = valtix_policy_rule_set.egress_policy_rule_set.rule_set_id
  region                  = var.region
  vpc_id                  = valtix_service_vpc.svpc1.vpc_id
}
```

For HUB mode Egress/East-West Gateway set the `security_type = EGRESS`

### GCP Gateway (EDGE Mode)
```hcl
resource "valtix_gateway" "gcp-gw" {
  name                      = "gcp-gw"
  description               = "GCP gateway"
  csp_account_name          = valtix_cloud_account.gcp_act.name
  instance_type             = "GCP_E2_8"
  gateway_image             = "22.06-04"
  gateway_state             = "ACTIVE"
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

### GCP Gateway (HUB Mode - Service VPC Managed by Valtix)
```hcl
resource "valtix_gateway" "gcp-gw1" {
  name                      = "gcp-gw"
  description               = "GCP gateway"
  csp_account_name          = valtix_cloud_account.gcp_act.name
  instance_type             = "GCP_E2_8"
  gateway_image             = var.gateway_image
  gateway_state             = "ACTIVE"
  mode                      = "HUB"
  security_type             = "EGRESS"
  policy_rule_set_id        = valtix_policy_rule_set.ingress_policy_rule_set.rule_set_id
  gcp_service_account_email = "valtix-controller@gcp-project.iam.gserviceaccount.com"
  region                    = "us-east1"
  vpc_id                    = valtix_service_vpc.service_vpc.id
}
```

For HUB mode INGRESS Gateway set the `security_type = INGRESS`

## Argument Reference

* `name` - (Required) Name of the Valtix Gateway.
* `description` - (Optional) Description of the Valtix Gateway
* `csp_account_name` - (Required) The CSP account where the Gateway will be deployed.
* `instance_type` - (Required) Must be one of:
    * **GCP_E2_8**
    * **AWS_M5_2XLARGE**
    * **AZURE_D8S_V3**
* `gateway_image` - (Required) Example `2.11-08`. This is the Valtix image version to be deployed for this Gateway. A list of applicable Gateway image versions is available from the Valtix Portal (ADMINISTRATION -> Management -> System -> Gateway Images). Please view the [Valtix Release Recommendation](https://docs.valtix.com/releases/recommendation/) for the recommended Gateway release or contact Valtix Support.
* `mode` - (AWS, Azure, GCP - Required) `EDGE` or `HUB`. Look into product documentation for different deployment modes.  This argument is not supported for OCI and must not be used.
* `security_type` - (Optional) `INGRESS` or `EGRESS`. If not specified, the default is `INGRESS`
* `gateway_state` - (Optional) Specifies the state of the Valtix Gateway.  When set to `ACTIVE`, the Gateway will be active and operational.  When set to `INACTIVE`, the Gateway will be disabled and not operational.  If not specified, the default is `ACTIVE`.
* `wait_for_gateway_state` - (Optional) Determines if Terraform should wait for the Gateway state, defined by the `gateway_state` argument, to be achieved before completing.  Applicable values are `true` and `false`.  If not specified, the default value is `true`.
* `policy_rule_set_id` - (Required) Rule set id of valtix_policy_rule_set. *(e.g. valtix_policy_rule_set.ruleset1.rule_set_id)*
* `ssh_key_pair` - (AWS - Required) SSH key pair name that's already in your AWS account
* `ssh_public_key` - (Azure - Required) Contents of SSH public key
* `gcp_service_account_email` - (GCP - Required) This is the GCP Gateway service account email, that provides permissions for the Valtix Gateway to integrate with other GCP project resources such as Secrets Manager and storage buckets. 
* `aws_iam_role_firewall` - (AWS - Required) This is the IAM role that's assigned to the Valtix firewall instances.
* `azure_user_identity_id` - (Azure - Optional) User assigned identity This is the IAM role that's assigned to the Valtix firewall instances.
* `azure_resource_group` - (Azure - Required) Azure resource group name used for all Valtix Gateway resources
* `region` - (Required) Region where the Valtix Gateway is deployed.
* `vpc_id` - (Required) VPC ID where the Valtix Gateway is deployed and is used for data traffic to be inspected. This must be either the VPC where you apps run or the shared services VPC that's peered (or hub via Transit Gateway) to other spoke (app) VPCs.  Please note that for HUB mode, this vpc_id must refer to **id** attribute that is exported using the [valtix_service_vpc](/terraform/valtix_service_vpc/#valtix_service_vpc) resource
* `aws_gateway_lb` - (AWS - Optional) `true` or `false`. This argument only applies to Gateway deployments in AWS with `mode` set to `HUB` and `security_type` set to `EGRESS`. If the argument is set to `true`, the Gateway will be deployed using an AWS Gateway Load Balancer (GWLB).  If not specified, the default value is `false` and the Gateway will be deployed using an AWS Network Load Balancer, which is a legacy deployment mode prior to AWS offering the GWLB.  (Even though this argument is optional, it is recommended to specify a value explicitly, as the default value may change in the future).
* `mgmt_vpc_id` - (GCP - Required) GCP VPC ID where the management interface of the Valtix Gateway is attached.
* `mgmt_security_group` - (Required except for AWS HUB mode) Security group ID for management traffic or GCP network tag to be used to define GCP firewall rules for Valtix firewall instances to communicate with the Valtix controller. This must allow all outbound access from Valtix management interface
* `datapath_security_group` - (Required except for AWS HUB mode) Security group ID for the datapath traffic (application traffic) or GCP network tag to be used to define GCP firewall rules for application traffic to pass through the Valtix Gateway. This must allow traffic to the ports that are defined as services on the Valtix controller
* `min_instances` - (Optional) Minimum number of instances per zone (default 1)
* `max_instances` - (Optional) Maximum number of instances per zone (default 1)
* `health_check_port` - (Optional) Port number that NLB uses to monitor the instances (default 65534). A rule must be configured on the datapath_security_group to allow traffic to this port.
* `log_profile` - (Optional) Log Profile ID *(e.g. valtix_profile_log_forwarding.splunk1.profile_id)*
* `packet_capture_profile` - (Optional) Packet Profile ID *(e.g. valtix_profile_packet_capture.pcap1.profile_id)*
* `diagnostics_profile` - (Optional) Diagnostics Profile ID *(e.g. valtix_profile_diagnostics.diag1.profile_id)*
* `settings` - (Optional) Gateway settings. This block can be repeated multiple times. Please check [this section](#gateway-settings) for the structure.
* `tags` - (Optional) User-defined Tags. This is a map of one or more user-defined key/value pairs that will be applied to each instantiated Gateway instance. The key is an unquoted name and the value is a quoted string.  Please check [this section](#gateway-tags) for the structure.  The Valtix Controller will add a Tag with keys of `Name` and `valtix_acct` during Gateway orchestration.  If a user-defined tag for either of those keys is specified, the user-defined values will used in place of the Controller-defined values.
* `instance_details` - (Required - EDGE Mode) This block is only needed when deploying a Gateway in EDGE mode.  This block should not be used when deploying a Gateway in HUB mode.  For EDGE mode deployment, the block can be repeated multiple times for deploying Gateway instances in multiple Availability Zones.  Look below for the [structure](#instance-details) of this block.  In EDGE mode, at least 1 block must be provided.
* `gateway_lb_integration` - (AWS - Optional) This block is required when integrating the Valtix Gateway with the AWS Global Accelerator. The block can be repeated multiple times for integrating with multiple Global Accelerators. See below for the [structure](#global-accelerator) of this block.

## Instance Details
This section is not required for AWS/Azure HUB mode as instance details are obtained from service VPC referenced in vpc_id attribute

* `availability_zone` - (Required) Specifies the availability zone where the Valtix Gateway instance(s) are deployed
* `mgmt_subnet` - (Required) Specifies the VPC subnet ID used for management traffic where the Valtix Gateway instance(s) are deployed for this availability zone.
* `datapath_subnet` - (Required) Specifies the VPC subnet ID used for data traffic where the Valtix Gateway instance(s) are deployed for this availability zone.

## Global Accelerator
This section is only required for AWS/Global Accelerator integration in ingress protection mode

* `type` - (Required) Set to `AWS_GLOBAL_ACCELERATOR`
* `name` - (Optional) The Valtix name of the Global Accelerator
* `awsga_resource_arn` - (Required) The AWS ARN for the Global Accelerator
* `awsga_endpoint_group_arn` - (Required) The AWS ARN for the Global Accelerator Endpoint Group
* `awsga_resource_name` - (Optional) The AWS name of the Global Accelerator
* `awsga_resource_fqdn` - (Required) The AWS FQDN of the Global Accelerator

### To integrate with an AWS Global Accelerator
```hcl
gateway_lb_integration {
  type = "AWS_GLOBAL_ACCELERATOR"
  name = "listener1"
  awsga_resource_arn = "arn:aws:globalaccelerator::902505820678:accelerator/d0c8cd60-e90c-4bb6-814e-6783716e1149"
  awsga_endpoint_group_arn = "arn:aws:globalaccelerator::902505820678:accelerator/d0c8cd60-e90c-4bb6-814e-6783716e1149/listener/80dab0fc/endpoint-group/d8c9bc581002"
  awsga_resource_name = "ga-for-hub"
  awsga_resource_fqdn = "a8ba3455f59690717.awsglobalaccelerator.com"
}
```

## Gateway Settings
Gateway settings define a list of settings that applies to the given Gateway

### To enable EBS encryption for the Gateway instances using default KMS key
```hcl
settings {
  name  = "gateway.aws.ebs.encryption.key.default"
  value = true
}
```

~> **Note on EBS/Disk Encryption setting using default CSP key**
The EBS Encryption Gateway setting using default KMS key only applies to Gateways deployed in AWS.  There is no need to use this setting to enable Disk Encryption for a Gateway deployed in Azure or GCP.  For AWS, EBS Encryption is disabled by default and the Gateway settings are needed to enable.  In Azure and GCP, Disk Encryption is enabled by default using a CSP key and cannot be disabled.

### To enable EBS Encryption for the Gateway instances using specified KMS key (AWS)
```hcl
settings {
  name  = "gateway.aws.ebs.encryption.key.customer_key"
  value = "<KMS key ID>"
}
```

### To enable Disk Encryption for the Gateway instances using specified KMS key (Azure)
```hcl
settings {
  name  = "gateway.azure.disk.encryption.key.customer_key"
  value = "<Disk Encryption Set Path>"
}
```

### To enable Disk Encryption for the Gateway instances using specified KMS key (GCP)
```hcl
settings {
  name  = "gateway.gcp.disk.encryption.key.customer_key"
  value = "<Crypto Key Path>"
}
```

~> **Note on EBS/Disk Encryption setting using a Customer Managed Encryption Key (CMEK)**
The EBS/Disk Encryption Gateway setting can use a Customer Managed Encryption Key (CMEK).  The key specified is created in specific locations related to each CSP.  For AWS, the key is created in the Key Management System (KMS) and referenced by its ID.  For Azure, the key is created in the Disk Encryption Sets and referenced by its full path.  The Azure Access control (IAM) Role for the Valtix Controller (valtix-controller-role) requires an additional permission: `Microsoft.Compute/sshPublicKeys/read`.  This permission has been updated in the Azure PowerShell script used to prepare the Subscription for onboarding into Valtix.  For GCP, the key is created as a Key Management Key Ring and referenced by its relative path.  The Role / Principal for Cloud KMS CryptoKey Encrypter / Decyptor requires a Google Managed Service Account (Service Agent) to be specified: Compute Engine Service Agent (`service-PROJECT_NUMBER@compute-system.iam.gserviceaccount.com`). 

### To override the default DNS Server IP Address used by the Management interface of an Azure Gateway
```hcl
settings {
  name  = "controller.gateway.dns_server_ip_address"
  value = "8.8.8.8"
}
```
~> **Note on DNS Server IP Address setting**
The DNS Server IP address setting only applies to Gateways deployed in Azure.  It is a user-specified override for the Valtix Gateway management interface (VNic).  The DNS specified should be a single IP referencing a DNS that can resolve publicly accessible domains.  When specified, the user-defined DNS will be used for any DNS resolution required by the Management traffic.  When not specified, the Management DNS will be taken from the VNet DNS setting.

### To deploy the AWS Gateway instances into private subnets with no public IPs assigned to the interfaces
```hcl
settings {
  name = "controller.gateway.assign_public_ip"
  value = false
}
```

~> **Note on Assign Public IP setting**
The Assign Public IP setting only applies to Gateways deployed in AWS using Edge Mode deployment.  Gateways in AWS deployed using Hub Mode deployment are either deployed as public if the orchestrated VPC is deployed without a NAT Gateway or deployed as private if the orchestrated VPC is deployed with a NAT Gateway.

### To change the AWS Gateway Load Balancer (GWLB) Acceptance Required
```hcl
settings {
  name  = "controller.gateway.aws.gwlb.acceptance_required"
  value = true
}
```

~> **Note on GWLB Acceptance Required**
The GWLB Acceptance required default is set to `false` when Valtix orchestrates the GWLB. In the case where a user will configure principals and control Endpoint connection acceptance using the AWS Console or AWS Terraform Provider it is desired for the Acceptance required to be set to `true`.

## Gateway Tags
Gateway tags define a map of Tags that will apply to each Gateway instance when instantiated

### To add a map of user-specified Tags to the Gateway instances
```hcl
tags = {
  tag1 = "value1"
  tag2 = "value2"
}
```

## Attribute Reference
* `gateway_gwlb_endpoints` - (AWS only) AWS Gateway Load Balancer endpoints created in each of the AZs displayed in the format as follows:

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

* `gwlb_service_name` - (AWS only) VPC Endpoint Service Name associated with the AWS Gateway Load Balancer.  This name can be used by the AWS Terraform Provider for establishing a GWLB Endpoint connection.

* `gwlb_service_id` - (AWS only) VPC Endpoint Service ID associated with the AWS Gateway Load Balancer.  This ID can be used by the AWS Terraform Provider for accepting GWLB Endpoint connections and assigning Principals.

* `gateway_endpoint` - For Gateways of `security_type = INGRESS`, this represents the NLB endpoint (FQDN, IP Address) to be used as the target for the client communicating with any application protected by the Valtix Ingress Gateway.  This information is most often used in a DNS A record (IP Address) or CNAME record (FQDN) to resolve the application FQDN to the Valtix Ingress Gateway endpoint.  Valtix will receive traffic on this endpoint and proxy the traffic to the appropriate backend application based on the configured policy.  For the Ingress Gateway, this attribute is populated for Gateways deployed in all CSPs (AWS, Azure, GCP, OCI).  For Gateways of `security_type = EGRESS`, this represents the NLB endpoint (IP Address) to be used as a target for routing traffic from the Spoke VPC/VNet/VCN to the Valtix Egress / East-West Gateway.  Valtix will receive traffic from clients, and forward or proxy the traffic to the appropriate destination based on the configured policy.  For the Egress / East-West Gateway, this attribute is only populated for non-AWS Gateways (Azure, GCP, OCI).  For the AWS Gateways, traffic is routed to the AWS Transit Gateway (TGW) or Gateway Load Balancer (GWLB) endpoints.