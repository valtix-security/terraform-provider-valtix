# Resource: valtix_gateway
Resource for creating and managing Valtix Gateways

~> **Note on Valtix Gateway Policy**
The [`valtix_cloud_account`](../valtix_cloud_account) and [`valtix_policy_rule_set`](../valtix_policy_rule_set)
resources must be created before the `valtix_gateway` resource can be created

~> **Note on Shared VPC Resources**
The Valtix Gateway resource (`valtix_gateway`) can be deployed using Subnets of a VPC that is shared from another Project.  The Gateway instances themselves are deployed in the local Project, while the interfaces use the Subnets of the Shared VPC.  When the VPC subnets are shared, the check to confirm the shared VPC is valid, and the subnets are contained within, is not possible.  Valtix has removed the check altogether.  It is important that the shared VPC ID specified is correct and valid to ensure proper Gateway deployment.<br><br>
It is also required that the Project that is sharing the VPC needs to grant *Compute Network User* access to the Valtix Controller service principal associated with Project the VPC is shared with.

## Example Usage

### AWS Gateway (INGRESS Gateway in EDGE Mode)
```hcl
resource "valtix_gateway" "aws_gw1" {
  name                    = "aws-gw1"
  description             = "AWS Ingress Gateway 1"
  csp_account_name        = valtix_cloud_account.aws_act.name
  instance_type           = "AWS_M5_2XLARGE"
  gateway_image           = var.gateway_image
  gateway_state           = "ACTIVE"
  mode                    = "EDGE"
  security_type           = "INGRESS"
  policy_rule_set_id      = valtix_policy_rule_set.ingress_policy_rule_set.id
  ssh_key_pair            = "ssh_keypair1"
  aws_iam_role_firewall   = "valtix-gateway-role"
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

For EDGE mode EGRESS Gateway specify `security_type = EGRESS` and add `aws_gateway_lb = true`

### AWS Gateway (EGRESS Gateway in HUB Mode)
```hcl
resource "valtix_gateway" "aws_hub_gw1" {
  name                   = "aws-hub-gw1"
  description            = "AWS Egress Gateway 1"
  csp_account_name       = valtix_cloud_account.aws_act.name
  instance_type          = "AWS_M5_2XLARGE"
  gateway_image          = var.gateway_image
  gateway_state          = "ACTIVE"
  mode                   = "HUB"
  security_type          = "EGRESS"
  policy_rule_set_id     = valtix_policy_rule_set.egress_policy_rule_set.id
  ssh_key_pair           = "ssh_keypair1"
  aws_iam_role_firewall  = "valtix_gateway_role"
  region                 = "us-east-1"
  vpc_id                 = valtix_service_vpc.service_vpc.id
  log_profile            = valtix_profile_log_forwarding.datadog.id
  packet_capture_profile = valtix_profile_packet_capture.awspcap1.id
  aws_gateway_lb         = true
}
```

For HUB mode INGRESS Gateway specify `security_type = INGRESS` and remove `aws_gateway_lb` argument

### Azure Gateway (INGRESS Gateway in EDGE Mode)
```hcl
resource "valtix_gateway" azure_gw1 {
  name                    = "azure-gw1"
  description             = "Azure Ingress Gateway 1"
  csp_account_name        = valtix_cloud_account.azure_act.name
  instance_type           = "AZURE_D8S_V3"
  azure_resource_group    = "rg1"
  gateway_image           = var.gateway_image
  gateway_state           = "ACTIVE"
  mode                    = "EDGE"
  security_type           = "INGRESS"
  ssh_public_key          = file(var.ssh_public_key_file)
  azure_user_name         = "centos"
  policy_rule_set_id      = valtix_policy_rule_set.egress_policy_rule_set.id
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

For EDGE mode EGRESS Gateway specify `security_type = EGRESS`

### Azure Gateway (INGRESS Gateway in HUB Mode)
```hcl
resource "valtix_gateway" "azure_hub_gw1" {
  name                 = "azure-hub-gw1"
  description          = "Azure Ingress Gateway 1"
  csp_account_name     = valtix_cloud_account.azure_act.name
  instance_type        = "AZURE_D8S_V3"
  azure_resource_group = "rg1"
  gateway_image        = var.gateway_image
  gateway_state        = "ACTIVE"
  mode                 = "HUB"
  security_type        = "INGRESS"
  ssh_public_key       = file(var.ssh_public_key_file)
  azure_user_name      = "centos"
  policy_rule_set_id   = valtix_policy_rule_set.egress_policy_rule_set.id
  region               = var.region
  vpc_id               = valtix_service_vpc.svpc1.id
}
```

For HUB mode EGRESS Gateway specify `security_type = EGRESS`

### GCP Gateway (INGRESS Gateway in EDGE Mode)
```hcl
resource "valtix_gateway" "gcp_gw1" {
  name                      = "gcp-gw1"
  description               = "GCP Ingress Gateway 1"
  csp_account_name          = valtix_cloud_account.gcp_act.name
  instance_type             = "GCP_E2_8"
  gateway_image             = var.gateway_image
  gateway_state             = "ACTIVE"
  mode                      = "EDGE"
  security_type             = "INGRESS"
  policy_rule_set_id        = valtix_policy_rule_set.ingress_policy_rule_set.id
  gcp_service_account_email = "valtix-controller@gcp-project.iam.gserviceaccount.com"
  region                    = "us-west1"
  mgmt_vpc_id               = "projects/gcp-project/global/networks/mgmt-vpc"
  vpc_id                    = "projects/gcp-project/global/networks/datapath-vpc"
  # mgmt_vpc_id             = data.google_compute_network.mgmt_vpc.self_link
  # vpc_id                  = data.google_compute_network.datapath_vpc.self_link
  mgmt_security_group       = "valtix-mgmt"
  datapath_security_group   = "valtix-datapath"
  log_profile               = valtix_profile_log_forwarding.splunk.id
  instance_details {
    availability_zone = "us-west1-a"
    mgmt_subnet       = "projects/gcp-project/regions/us-west1/subnetworks/mgmt-subnet"
    datapath_subnet   = "projects/gcp-project/regions/us-west1/subnetworks/datapath-subnet"
    # mgmt_subnet     = data.google_compute_subnetwork.mgmt_subnet1.self_link
    # datapath_subnet = data.google_compute_subnetwork.datapath_subnet1.self_link
  }
  instance_details {
    availability_zone = "us-west1-b"
    mgmt_subnet       = "projects/gcp-project/regions/us-west1/subnetworks/mgmt-subnet"
    datapath_subnet   = "projects/gcp-project/regions/us-west1/subnetworks/datapath-subnet"
    # mgmt_subnet     = data.google_compute_subnetwork.mgmt_subnet2.self_link
    # datapath_subnet = data.google_compute_subnetwork.datapath_subnet2.self_link
  }
}
```

For EDGE mode EGRESS Gateway set the `security_type = EGRESS`

### GCP Gateway (EGRESS Gateway in HUB Mode)
```hcl
resource "valtix_gateway" "gcp_hub_gw1" {
  name                      = "gcp-hub-gw1"
  description               = "GCP Egress Gateway 1"
  csp_account_name          = valtix_cloud_account.gcp_act.name
  instance_type             = "GCP_E2_8"
  gateway_image             = var.gateway_image
  gateway_state             = "ACTIVE"
  mode                      = "HUB"
  security_type             = "EGRESS"
  policy_rule_set_id        = valtix_policy_rule_set.ingress_policy_rule_set.id
  gcp_service_account_email = "valtix-controller@gcp-project.iam.gserviceaccount.com"
  region                    = "us-east1"
  vpc_id                    = valtix_service_vpc.service_vpc.id
}
```

For HUB mode INGRESS Gateway set the `security_type = INGRESS`

### OCI Gateway (INGRESS Gateway in EDGE Mode)
```hcl
resource "valtix_gateway" "oci_gw1" {
  name                    = "oci-gw1"
  description             = "OCI Ingress Gateway 1"
  csp_account_name        = valtix_cloud_account.oci_act.name
  instance_type           = "OCI_E3_FLEX"
  gateway_image           = var.gateway_image
  gateway_state           = "ACTIVE"
  mode                    = "EDGE"
  security_type           = "INGRESS"
  ssh_public_key          = file(var.ssh_public_key_file)
  policy_rule_set_id      = valtix_policy_rule_set.ingress_policy_rule_set.id
  oci_compartment_id      = "ocid1.compartment.oc1..aaaaaaaauo3cpjxzlgebu3zmzsirphib4mtpzgxa4ca2rbzhlbwokpzrjixa"
  region                  = "us-sanjose-1"
  vpc_id                  = "ocid1.vcn.oc1.us-sanjose-1.amaaaaaa725octaa5u3m3po74swbme7p274xpqw43af6u5vjogpmpeugzezq"
  mgmt_security_group     = "ocid1.networksecuritygroup.oc1.us-sanjose-1.aaaaaaaa2m3q2h3yfaawze4tdtyc5frwoi4kpsbppbq7lyee4jprimxvuvmq"
  datapath_security_group = "ocid1.networksecuritygroup.oc1.us-sanjose-1.aaaaaaaai62ierhdjsb5v3v447wzgxzihl6nx7b3xqyrjy2qixz5uxylyl6a"
  instance_details {
    availability_zone = "RVji:US-SANJOSE-1-AD-1"
    mgmt_subnet       = "ocid1.subnet.oc1.us-sanjose-1.aaaaaaaaxpi7fbtx2xe2evsrlsygtv2crtqqg3dkpw5m2jskfpqm36nygj4a"
    datapath_subnet   = "ocid1.subnet.oc1.us-sanjose-1.aaaaaaaaukdyjnjwooqhrig66dhkbplunhviyzrkvheleqoad7jrojk2m5ea"
  }
}
```

For EDGE mode EGRESS Gateway set the `security_type = EGRESS`

## Argument Reference

* `name` - (Required) Name of the Gateway
* `description` - (Optional) Description of the Gateway
* `csp_account_name` - (Required) The CSP account where the Gateway will be deployed
* `instance_type` - (Required) The instance type used when deploying the Gateway.  Applicable CSP-specific values are:
    * **AWS**
        * **M5**
            * **AWS_M5_2XLARGE** (8 core)
            * **AWS_M5_XLARGE** (4 core)
            * **AWS_M5_LARGE** (2 core)
        * **M7i**
            * **AWS_M7I_2XLARGE** (8 core)
            * **AWS_M7I_XLARGE** (4 core)
            * **AWS_M7I_LARGE** (2 core)
    * **Azure**
        * **DS_V3**
            * **AZURE_D8S_V3** (8 core)
            * **AZURE_D4S_V3** (4 core)
            * **AZURE_D2S_V3** (2 core)
        * **DS_V5**
            * **AZURE_D8S_V5** (8 core)
            * **AZURE_D4S_V5** (4 core)
            * **AZURE_D2S_V5** (2 core)
    * **GCP**
        * **E2**
            * **GCP_E2_8** (8 core)
            * **GCP_E2_4** (4 core)
            * **GCP_E2_2** (2 core)
    * **OCI**
        * **E3**
            * **OCI_E3_FLEX** (8 core)
* `gateway_image` - (Required) Represents the image version to be used for this Gateway. A list of applicable image versions is available from the Valtix Portal (ADMINISTRATION -> Management -> System -> Gateway Images). Please view the [Valtix Release Recommendation](https://docs.valtix.com/releases/recommendation/) for the recommended Gateway image version or contact [Valtix Support](mailto:support@valtix.com).
* `mode` - (Required - AWS, Azure, GCP) Applicable values are `EDGE` or `HUB`. Review the Valtix product documentation for information on the different deployment modes.  This argument is not supported for OCI and must not be used.
* `security_type` - (Optional) Applicable values are `INGRESS` or `EGRESS`. If not specified, the default is `INGRESS`.
* `gateway_state` - (Optional) Specifies the state of the Gateway.  Applicable values are `ACTIVE` and `INACTIVE`.  When set to `ACTIVE`, the Gateway will be enabled and operational.  When set to `INACTIVE`, the Gateway will be disabled and not operational.  If not specified, the default is `ACTIVE`.
* `wait_for_gateway_state` - (Optional) Determines if Terraform should wait for the Gateway state, defined by the `gateway_state` argument, to be achieved before completing.  Applicable values are `true` and `false`.  If not specified, the default value is `true`.
* `policy_rule_set_id` - (Required) Rule set ID of valtix_policy_rule_set. *(e.g. valtix_policy_rule_set.ruleset1.id)*
* `ssh_key_pair` - (Required - AWS, Azure) Name of the SSH Key Pair created within the AWS Account or Azure Subscription.  The CSP Key Pairs are Regional constructs and must be created in the same Region as specified by the `region` argument where the Gateway will be deployed.
* `ssh_public_key` - (Required - Azure, GCP) The SSH public key to be assigned to the Gateway instances. Must be *ssh-rsa* only.
* `azure_user_name` - (Optional - Azure) Name to use as the user when SSH to a Azure Gateway instance.  When not specified, `centos` is used.
* `gcp_user_name` - (Optional - GCP) Name to use as the user when SSH to a GCP Gateway instance.  When not specified, `centos` is used.
* `gcp_service_account_email` - (Required - GCP) The GCP Service Account Email that defines the permissions for the Gateway to integrate with other GCP Project resources such as Secrets Manager and Storage Buckets. 
* `aws_iam_role_firewall` - (Required - AWS) The AWS IAM role that defines the permissions for the Gateway to integrate with other AWS Account resources such as Key Pairs, Secrets Manager and Key Management Service (KMS).
* `azure_user_identity_id` - (Optional - Azure) The Azure User Assigned Identity that defines the permissions for the Gateway to integrate with other Azure Subscription resources such as Key Pairs, Key Vault and Blob Storage. The value specified should be the resource full path.
* `azure_resource_group` - (Required - Azure) Azure Resource Group name used to associate all created Gateway resources
* `oci_compartment_id` - (Required - OCI) OCI Compartment ID where the Gateway will be deployed
* `region` - (Required) Region where the Gateway will be deployed
* `vpc_id` - (Required) VPC/VNet/VNC where the Gateway will be deployed. For HUB mode deployments, the value must refer to the `id` attribute of the [`valtix_service_vpc`](/terraform/valtix_service_vpc/#valtix_service_vpc) resource.  For EDGE mode deployments, the CSP-specific resource should be used:  For AWS, use the VPC resource ID; For Azure, use the VNet resource full path; For GCP, use the VPC resource self-link; For OCI, use the VNC resource OCID.
* `aws_gateway_lb` - (Optional - AWS) `true` or `false`. This argument only applies to Gateway deployments in AWS with `security_type` set to `EGRESS`. If the argument is set to `true`, the Gateway will be deployed using an AWS Gateway Load Balancer (GWLB).  If the argument is set to `false`, the Gateway will be deployed using an internal AWS Network Load Balancer (NLB), which is a legacy deployment mode prior to AWS offering the GWLB.  If not specified, the default value is `true`.
* `azure_gateway_lb` - (Optional - Azure) *[Public Preview]* `true` or `false`. This argument only applies to Gateway deployments in Azure with `security_type` set to `INGRESS`. If the argument is set to `true`, the Gateway will be deployed using an Azure Gateway Load Balancer (GWLB).  If not specified, the default value is `false` and the Gateway will be deployed using an Internet-facing Azure Network Load Balancer (NLB), which is the default deployment for an Ingress Gateway in Azure.
* `mgmt_vpc_id` - (Required - GCP EDGE Mode) Self link of the GCP VPC where the management interface of the Gateway is attached
* `mgmt_security_group` - (Required - EDGE Mode) AWS Security Group ID, Azure Network Security Group ID, GCP Network Tag name assigned to the management interface, or OCI Security Group OCID to permit management traffic to egress the Gateway. This must allow all outbound traffic for the Gateway to communicate with the Valtix Controller, Valtix S3 Bucket and for DNS resolution.
* `datapath_security_group` - (Required - EDGE Mode) AWS Security Group ID, Azure Network Security Group ID, GCP Network Tag name assigned to the datapath interface, or OCI Security Group OCID to permit datapath traffic to ingress and egress the Gateway. It's recommended to leave this open so that all traffic can be sent and received by the Gateway where the Gateway Policy will control whether traffic is allowed or denied.
* `min_instances` - (Optional) Minimum number of instances per availability zone.  If not specified, the default value is `1`.
* `max_instances` - (Optional) Maximum number of instances per availability zone.  If not specified, the default value is `3`.
* `health_check_port` - (Optional) TCP Port number that the Valtix orchestrated load balancers use for health checks to the Gateway instances.  If not specified, the default value is `65534`. A rule must be configured on the `datapath_security_group` to allow traffic to this TCP Port.
* `log_profile` - (Optional) Log Forwarding Profile ID *(e.g. valtix_profile_log_forwarding.splunk1.id)*
* `packet_capture_profile` - (Optional) Packet Capture Profile ID *(e.g. valtix_profile_packet_capture.pcap1.id)*
* `ntp_profile` - (Optional) NTP Profile ID *(e.g. valtix_profile_ntp.ntp1.id)*
* `metrics_profile` - (Optional) Metrics Forwarding Profile ID *(e.g. valtix_profile_metrics_forwarding.datadog.id)*
* `settings` - (Optional) Gateway Settings block. This block can be repeated multiple times. See [Gateway Settings](#gateway-settings) for the block structure.
* `tags` - (Optional) User-defined Tags. This is a map of one or more user-defined key/value pairs that will be applied to each Gateway instance. The key is an unquoted name and the value is a quoted string.  See [Gateway Tags](#gateway-tags) for the block structure.  The Valtix Controller will add a Tag with keys of `Name` and `valtix_acct` during Gateway orchestration.  If a user-defined tag for either of those keys is specified, the user-defined values will used in place of the Controller-defined values.
* `instance_details` - (Required - EDGE Mode) Gateway Instance Details.  This block is only needed when deploying a Gateway in EDGE mode.  This block should not be used when deploying a Gateway in HUB mode.  For EDGE mode deployment, the block can be repeated multiple times for deploying Gateway instances in multiple Availability Zones.  See [Instance Details](#instance-details) for the block structure.  In EDGE mode, at least 1 block must be provided.
* `gateway_lb_integration` - (Optional - AWS) AWS Global Accelerator integration.  This block is used when integrating the Gateway with the AWS Global Accelerator. The block can be repeated multiple times for integrating with multiple Global Accelerators. See [Global Accelerator](#global-accelerator) for the block structure.

## Instance Details
This section is not required for AWS/Azure HUB mode as instance details are obtained from service VPC referenced in vpc_id attribute

* `availability_zone` - (Required) Specifies the Availability Zone where the Gateway instance will be deployed
* `mgmt_subnet` - (Required) Specifies the VPC/VNet Subnet ID used to deploy the Gateway management interface within the specified Availability Zone
* `datapath_subnet` - (Required) Specifies the VPC/VNet Subnet ID used to deploy the Gateway datapath interface within the specified Availability Zone

## Global Accelerator
This section is used for AWS Global Accelerator integration. Applies only to an Ingress Gateway deployed in AWS.

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
Gateway Settings are set of configurable settings that can be specified by the user and applied to the Gateway and its corresponding Gateway Instances.  If a change to the settings from their defaults are necessary, the individual setting and the configured value can be applied to the list of settings as part of the settings block of the Gateway resource.

### Gateway AWS/Azure/GCP EBS/Disk Encryption Settings

#### To enable AWS EBS Encryption using default KMS Key
```hcl
settings {
  name  = "gateway.aws.ebs.encryption.key.default"
  value = true
}
```

#### To enable AWS EBS Encryption using specified KMS Key
```hcl
settings {
  name  = "gateway.aws.ebs.encryption.key.customer_key"
  value = "<KMS key ID>"
}

settings {
  name  = "gateway.aws.ebs.encryption.key.customer_key"
  value = "arn:aws:kms:us-east-1:1112223333:key/67b90d29-5083-4166-a111-bfc810340f7d"
}
```

#### To enable Azure Disk Encryption using specified KMS Key
```hcl
settings {
  name  = "gateway.azure.disk.encryption.key.customer_key"
  value = "<Disk Encryption Set Path>"
}

settings {
  name  = "gateway.azure.disk.encryption.key.customer_key"
  value = "/subscriptions/1111111-33f9-4f5c-86e4-222222222/resourceGroups/valtix-rg/providers/Microsoft.Compute/diskEncryptionSets/valtixDiskEncryptionSet"
}
```

#### To enable GCP Disk Encryption using specified Crypto Key
```hcl
settings {
  name  = "gateway.gcp.disk.encryption.key.customer_key"
  value = "<Crypto Key Path>"
}

settings {
  name  = "gateway.gcp.disk.encryption.key.customer_key"
  value = "projects/security-icon-111111/locations/us-east1/keyRings/valtix-disk-encryption/cryptoKeys/key1"
}
```

~> **Note on AWS/Azure/GCP EBS/Disk Encryption Setting using default CSP Key**
The EBS Encryption Gateway setting using default KMS key only applies to Gateways deployed in AWS.  There is no need to use this setting to enable Disk Encryption for a Gateway deployed in Azure or GCP.  For AWS, EBS Encryption is disabled by default and the Gateway settings are needed to enable.  In Azure and GCP, Disk Encryption is enabled by default using a CSP key and cannot be disabled.

~> **Note on AWS/Azure/GCP EBS/Disk Encryption Setting using a Customer Managed Encryption Key (CMEK)**
The EBS/Disk Encryption Gateway setting can use a Customer Managed Encryption Key (CMEK).  The key specified is created in specific locations related to each CSP.  For AWS, the key is created in the Key Management System (KMS) and referenced by its ID.  For Azure, the key is created in the Disk Encryption Sets and referenced by its full path.  The Azure Access control (IAM) Role for the Valtix Controller (valtix-controller-role) requires an additional permission: `Microsoft.Compute/sshPublicKeys/read`.  This permission has been updated in the Azure PowerShell script used to prepare the Subscription for onboarding into Valtix.  For GCP, the key is created as a Key Management Key Ring and referenced by its relative path.  The Role / Principal for Cloud KMS CryptoKey Encrypter / Decypter requires a Google Managed Service Account (Service Agent) to be specified: Compute Engine Service Agent (`service-PROJECT_NUMBER@compute-system.iam.gserviceaccount.com`). 

### Gateway DNS Server IP Address Setting

#### To override the default DNS Server IP Address used by the Management interface of an Azure Gateway
```hcl
settings {
  name  = "controller.gateway.dns_server_ip_address"
  value = "8.8.8.8"
}
```
~> **Note on DNS Server IP Address Setting**
The DNS Server IP address setting only applies to Gateways deployed in Azure.  It is a user-specified override for the Gateway management interface (VNic).  The DNS specified should be a single IP referencing a DNS that can resolve publicly accessible domains.  When specified, the user-defined DNS will be used for any DNS resolution required by the Management traffic.  When not specified, the Management DNS will be taken from the VNet DNS setting.

### Gateway Assign Public IP Setting

#### To deploy Gateway instances into private subnets with no public IPs assigned to the interfaces
```hcl
settings {
  name = "controller.gateway.assign_public_ip"
  value = false
}
```

~> **Note on Assign Public IP Setting**
The Assign Public IP setting applies to Gateways deployed in Edge Mode for AWS, Azure and GCP.  Gateways in AWS deployed in Hub Mode are either deployed as public if the orchestrated VPC is deployed without a NAT Gateway or deployed as private if the orchestrated VPC is deployed with a NAT Gateway. For Hub mode deployment in Azure and GCP, the VNet or VPC is orchestrate without the use of a Virtual NAT or Cloud NAT, and thus the Gateways are deployed as public.

### Gateway AWS GWLB Settings

#### To change the AWS GWLB Acceptance Required
```hcl
settings {
  name  = "controller.gateway.aws.gwlb.acceptance_required"
  value = true
}
```

~> **Note on GWLB Acceptance Required Setting**
The GWLB Acceptance required default is set to `false` when Valtix orchestrates the GWLB. In the case where a user will configure Principals and/or control Endpoint connection acceptance using the AWS Console or AWS Terraform Provider it is desired for the Acceptance required to be set to `true`.

#### To not deploy an AWS GWLB Endpoint (GLWBe) into the Datapath Subnet
```hcl
settings {
  name  = "controller.gateway.aws.gwlb.deploy_gwlb_endpoints"
  value = false
}
```

~> **Note on AWS Deploy GWLB Endpoint (GWLBe) Setting**
The GWLB Endpoint (GWLBe) creation default is set to `true` when Valtix orchestrates the GWLB when deploying an Egress Gateway. When not specified or explicitly set to `true`, Valtix orchestrates a GWLBe and connects it to the GWLB.  When set to `false`, Valtix will not orchestrate a GWLBe and will rely on the user to create any GWLBes using the AWS Terraform Provider or AWS Console and connect them to the GWLB.

#### To specify GWLB Service Principals (single Principal)
```hcl
settings {
  name  = "controller.gateway.aws.gwlb.service_principals"
  value = "*"
}
```

#### To specify GWLB Service Principals (multiple Principals)
```hcl
settings {
  name  = "controller.gateway.aws.gwlb.service_principals"
  value = <<EOT
    arn:aws:iam::902505820618:root
    arn:aws:iam::059126560514:root
  EOT
}
```

~> **Note on GWLB Service Principals Setting**
When Valtix deploys an Egress Gateway in AWS it orchestrates the creation of a GWLB and GWLB Service.  The default deployment for the GWLB is to not require acceptance.  If a user prefers to require acceptance, and control the GWLBe connections and acceptance using Service Principals, the GWLB Service Principals setting can be used.  The setting is a list of strings representing Service Principals.  The setting will attach the Service Principals to the GWLB Service.

### Gateway Proxy (HTTP) Keepalive Setting

#### To alter the Proxy (HTTP) Keepalive (default 5s)
```hcl
settings {
  name ="gateway.proxy.keepalive"
  value = 5
}
```

~> **Note on Proxy (HTTP) Keepalive Setting**
When HTTP Keepalive is used (controlled by a header setting on the application server), HTTP sessions will be reused.  In order to reduce the risk of session pool draining, each session will have an inactivity timeout of 5 seconds (default when not specified) after which point the session will be torn down. If a different timeout value is needed (e.g., if the distance between the clients and the application is substantial resulting in longer propagation delays, requiring a longer timeout), this setting can be used to change the value.

### Gateway FQDN IP Cache Settings

#### To alter the FQDN IP Cache Update Interval (default 60; range 3 - 60)
```hcl
settings {
  name ="gateway.fqdn_ip_cache.update_interval"
  value = 5
}
```

#### To alter the FQDN IP Cache IP Entry TTL (default 0; range 0 -  640000)
```hcl
settings {
  name ="gateway.fqdn_ip_cache.entry_ttl"
  value = 86400
}
```

#### To alter the FQDN IP Cache Size (default 0; range 0 - 5184000)
```hcl
settings {
  name ="gateway.fqdn_ip_cache.size"
  value = 320000
}
```

~> **Note on FQDN IP Cache Settings**
If the Address Object is configured with a set of FQDNs, the Valtix Gateway will resolve the FQDNs to a set of IPs using DNS.  The DNS resolution occurs every 60s and no cache is maintained (e.g., a new set of IPs will be established for each resolution).  If the FQDNs can resolve to a larger set of IPs, the Gateway can be configured to maintain an IP cache.  Gateway settings can be configured to control the DNS update interval (resolution frequency), entry TTL for each IP that is placed into the cache (duration the cache will maintain the IP before the IP is flushed), and the size of the cache (number of unique IPs that can be maintained by the cache).  To use FQDNs in an Address Object, see the [Address Object STATIC (Source Destination) Arguments](../resources/address_object#static-source-destination-arguments) section of the Address Object resource.

### Gateway GCP Internal Load Balancer IP Setting
```hcl
settings {
  name = "gateway.gcp.lb.ip_address_object_id"
  value = google_compute_address.internal_with_subnet_and_address.self_link
}
```

~> **Note on Gateway GCP Internal Load Balancer IP Setting**
When a Gateway resoure in GCP is created, a GCP internal Load Balancer (LB) is also created to front the Gateway instances.  This LB requires an internal IP address to be used as the endpoint for routing traffic to the LB. When this setting is not used, the Controller will orchestrate creating the IP resource and assigning it to the LB.  When using this setting, the IP address resource is created by the user and supplied to the Gateway resource for the Controller to assign to the created LB.  The IP address resource is a `google_compute_address` resource.  The `address_type` must be set to "INTERNAL", the `subnetwork` must be the same as the Gateway datapath subnet, and the `region` must be the same region as the Gateway.

### Gateway Instance Creation Retry Settings
```hcl
settings {
  name = "controller.gateway.instance_creation_retry_count"
  value = "3"
}
settings {
  name = "controller.gateway.instance_creation_retry_reset_timeout"
  value = "360"
}
```

~> **Note on Gateway Instance Creation Retry Settings**
When a Gateway resource is created, if an instance creation fails, the Controller will initiate a retry.  If the issue is corrected, then the Gateway will eventually be successfully created. If the issue is not corrected, then the Controller will retry indefinitely.  If the Controller continues to retry without success, Terraform will timeout after 20 minutes and produce an error stating the Gateway is not yet ACTIVE.

The Gateway Instance Creation Retry Settings provide a user with some control over the instance creation retry behavior.  The settings are defined and behave as follows:
* `controller.gateway.instance_creation_retry_count` - Defines the number of times the Controller will retry creating each instance.  If the retry count is exceeded, then the Controller will no longer retry the creation and the Gateway will remain in ACTIVE_PENDING state.  The Controller will reinitiate its retry attempts once the retry reset time has expired.  If this setting is not specified, the Controller will retry indefinitely.
* `controller.gateway.instance_creation_retry_reset_timeout` - Defines the amount of time (in minutes) after the retry count has been exceeded that the Controller will reinitiate its retry attempts. If this setting is not specified, the reset time will be infinite and the Controller will never reinitiate its retry attempts. This setting is only applicable if the retry count setting is specifed.

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
* `id` - ID of the Gateway resource
* `gateway_gwlb_endpoints` - (AWS, Azure) For AWS, this is represented as a list of maps, where each list entry is a AZ-specific endpoint, and each map are the attributes of the endpoint, including the AZ.  An example format is shown as follows:

    ```json
    "gateway_gwlb_endpoints": [
      {
        "availability_zone": "us-east-1a",
        "endpoint_id": "vpce-047c749fc6f7e0c0d",
        "network_interface_id": "eni-017eacdb23d2ebaf4",
        "subnet_id": "subnet-0d61750e97caafd9d"
      },
      {
        "availability_zone": "us-east-1b",
        "endpoint_id": "vpce-0707fa3f03c5064a7",
        "network_interface_id": "eni-020464bd838461bca",
        "subnet_id": "subnet-0fd61e07f200224f1"
      }
    ]
    ```
    For Azure, this is represented as a single entry list of a map, where the map contains the attributes of the endpoint.  An example format is shown as follows:

    ```json
    "gateway_gwlb_endpoints": [
      {
        "endpoint_id": "/subscriptions/8b29c730-36f9-4f5c-86e4-96129569d6a0/resourceGroups/hardik-eastus-resources/providers/Microsoft.Network/loadBalancers/valtix-l-azure-injkczap/frontendIPConfigurations/valtix-l-azure-injkczap-fip"
      }
    ]
    ```

* `gwlb_service_name` - (AWS only) VPC Endpoint Service Name associated with the AWS Gateway Load Balancer.  This name can be used by the AWS Terraform Provider for establishing a GWLB Endpoint connection.
* `gwlb_service_id` - (AWS only) VPC Endpoint Service ID associated with the AWS Gateway Load Balancer.  This ID can be used by the AWS Terraform Provider for accepting GWLB Endpoint connections and assigning Principals.
* `gateway_endpoint` - For Gateways of `security_type = INGRESS`, this represents the NLB endpoint (FQDN, IP Address) to be used as the target for the client communicating with any application protected by the Valtix Ingress Gateway.  This information is most often used in a DNS A record (IP Address) or CNAME record (FQDN) to resolve the application FQDN to the Valtix Ingress Gateway endpoint.  Valtix will receive traffic on this endpoint and proxy the traffic to the appropriate backend application based on the configured policy.  For the Ingress Gateway, this attribute is populated for Gateways deployed in all CSPs (AWS, Azure, GCP, OCI).  For Gateways of `security_type = EGRESS`, this represents the NLB endpoint (IP Address) to be used as a target for routing traffic from the Spoke VPC/VNet/VCN to the Valtix Egress / East-West Gateway.  Valtix will receive traffic from clients, and forward or proxy the traffic to the appropriate destination based on the configured policy.  For the Egress / East-West Gateway, this attribute is only populated for non-AWS Gateways (Azure, GCP, OCI).  For the AWS Gateways, traffic is routed to the AWS Transit Gateway (TGW) or Gateway Load Balancer (GWLB) endpoints.

## Import
Gateway resources can be imported using the resource `name`:

```hcl
$ terraform import valtix_gateway.aws-gw1 aws-gw1
```