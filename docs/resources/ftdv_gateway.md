# Resource: valtix_ftdv_gateway
Resource for creating and managing Firewall Threat Defense Virtual (FTDv) Gateways

## Example Usage

### AWS FTDv Gateway
```hcl
resource "valtix_ftdv_gateway" "aws_ftdv_gw1" {
	name                  = "aws-ftdv-gw1"
  description           = "AWS FTDv Gateway 1"
  csp_account_name      = valtix_cloud_account.aws_act.name
	region                = "us-west-2"
	vpc_id                = valtix_service_vpc.svpc1.id
	instance_type         = "AWS_C5_4XLARGE"
  aws_iam_role_firewall = "arn:aws:iam::407068433435:role/valtix-ftdv-gateway-role"
  ssh_key_pair          = "ssh_keypair1"
	ftdv_version          = "7.6.0"
  ftdv_policy_id        = "06B65657-5F61-0ed3-0000-004294985858"
	ftdv_password         = "<need to fill in>"
	ftdv_license_model    = "MULTICLOUD_DEFENSE"
}
```

### Azure FTDv Gateway
```hcl
resource "valtix_gateway" "azure_ftdv_gw1" {
  name                 = "azure-ftdv-gw1"
  description          = "Azure FTDv Gateway 1"
  csp_account_name     = valtix_cloud_account.azure_act.name
  region               = var.region
  vpc_id               = valtix_service_vpc.svpc1.id
  instance_type        = "AZURE_D8S_V3"
  azure_resource_group = "rg1"
  ssh_public_key       = file(var.ssh_public_key_file)
	ftdv_version         = "7.6.0"
  ftdv_policy_id       = "06B65657-5F61-0ed3-0000-004294985858"
	ftdv_password        = "<need to fill in>"
	ftdv_license_model   = "MULTICLOUD_DEFENSE"
}
```

## Argument Reference

* `name` - (Required) Name of the Gateway
* `description` - (Optional) Description of the Gateway
* `csp_account_name` - (Required) The CSP account where the Gateway will be deployed
* `region` - (Required) Region where the Gateway will be deployed
* `vpc_id` - (Required) VPC/VNet where the Gateway will be deployed. The value must refer to the `id` attribute of the [`valtix_service_vpc`](/terraform/valtix_service_vpc/#valtix_service_vpc) resource.
* `instance_type` - (Required) The instance type used when deploying the Gateway.  Applicable CSP-specific values are:
    * **AWS**
        * **C5**
            * **AWS_C5_4XLARGE** (16 core)
            * **AWS_C5_2XLARGE** (8 core)
            * **AWS_C5_XLARGE** (4 core)
    * **Azure**
        * **D5_V2**
            * **AZURE_D5_V2** (16 core)
            * **AZURE_D4_V2** (8 core)
            * **AZURE_D3_V2** (4 core)
* `min_instances` - (Optional) Minimum number of instances per availability zone.  If not specified, the default value is `1`.
* `max_instances` - (Optional) Maximum number of instances per availability zone.  If not specified, the default value is `3`.
* `aws_iam_role_firewall` - (Required - AWS) The AWS IAM role that defines the permissions for the Gateway to integrate with other AWS Account resources such as Key Pairs, Secrets Manager and Key Management Service (KMS).
* `azure_resource_group` - (Optional - Azure) Azure Resource Group name used to associate all created Gateway resources
* `azure_user_identity_id` - (Optional - Azure) The Azure User Assigned Identity that defines the permissions for the Gateway to integrate with other Azure Subscription resources such as Key Pairs, Key Vault and Blob Storage. The value specified should be the resource full path.
* `ssh_key_pair` - (Optional - AWS, Azure) Name of the SSH Key Pair created within the AWS Account or Azure Subscription.  The CSP Key Pairs are Regional constructs and must be created in the same Region as specified by the `region` argument where the Gateway will be deployed.
* `ssh_public_key` - (Optional - Azure) The SSH public key to be assigned to the Gateway instances. Must be *ssh-rsa* only.
* `health_check_port` - (Optional) TCP Port number that the Multicloud Defense orchestrated load balancers use for health checks to the Gateway instances.  If not specified, the default value is `65534`. A rule must be configured on the `datapath_security_group` to allow traffic to this TCP Port.
* `gateway_state` - (Optional) Specifies the state of the Gateway.  Applicable values are `ACTIVE` and `INACTIVE`.  When set to `ACTIVE`, the Gateway will be enabled and operational.  When set to `INACTIVE`, the Gateway will be disabled and not operational.  If not specified, the default is `ACTIVE`.
* `wait_for_gateway_state` - (Optional) Determines if Terraform should wait for the Gateway state, defined by the `gateway_state` argument, to be achieved before completing.  Applicable values are `true` and `false`.  If not specified, the default value is `true`.
* `ftdv_version` - (Required) Represents the image version to be used for this Gateway
* `ftdv_policy_id` - (Optional) Represents the ID of the policy to assign to the Gateway.  If not specified, the Multicloud Defense Controller will create a new policy and assign this policy to the Gateway.  This argument cannot be updated after Gateway deployment.  An update operation will be blocked.  Policy changes to the Gateway instances must be done within cdFMC.
* `ftpv_password` - (Required) The admin password to assign to each Gateway instance
* `ftdv_license_mode` - (Optional) The license mode to use for this Gateway.  Applicable values are: `MULTICLOUD_DEFENSE` or `SMART_LICENSING`.  If not specified, the default value is `SMART_LICENSE`.
* `ftdv_licenses` - (Optional) A comma-separated list of license types to use when the `ftdv_license_mode` is specified as `SMART_LICENSE`.  Applicable values are: `BASE`, `CARRIER`, `MALWARE`, `THREAT` and `URLFILTER`.  `BASE` is always required.  `THREAT` is required if also specifying `MALWARE`.  If not specified, the default value is `BASE`.
* `ftdv_perforemance_tier` - (Optional) The performance tier to use when the `ftdv_license_mode` is specified as `SMART_LICENSE`.  Applicable values are: For AWS, `FTDv20`, `FTDv30`, `FTDv50` or `FTDv100`.  For Azure, `FTDv5`, `FTDv10`, `FTDv20`, `FTDv30`, `FTDv50` or `FTDv100`.  If not specified, the default value is `FTDv50`.
* `tags` - (Optional) User-defined Tags. This is a map of one or more user-defined key/value pairs that will be applied to each Gateway instance. The key is an unquoted name and the value is a quoted string.  See [Gateway Tags](#gateway-tags) for the block structure.  The Multicloud Defense Controller will add a Tag with keys of `Name` and `valtix_acct` during Gateway orchestration.  If a user-defined tag for either of those keys is specified, the user-defined values will used in place of the Controller-defined values.
* `settings` - (Optional) Gateway Settings block. This block can be repeated multiple times. See [Gateway Settings](#gateway-settings) for the block structure.


## Gateway Settings
Gateway Settings are set of configurable settings that can be specified by the user and applied to the Gateway and its corresponding Gateway Instances.  If a change to the settings from their defaults are necessary, the individual setting and the configured value can be applied to the list of settings as part of the settings block of the Gateway resource.

### Gateway AWS/Azure EBS/Disk Encryption Settings

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
~> **Note on AWS/Azure EBS/Disk Encryption Setting using default CSP Key**
The EBS Encryption Gateway setting using default KMS key only applies to Gateways deployed in AWS.  There is no need to use this setting to enable Disk Encryption for a Gateway deployed in Azure.  For AWS, EBS Encryption is disabled by default and the Gateway settings are needed to enable.  In Azure, Disk Encryption is enabled by default using a CSP key and cannot be disabled.

~> **Note on AWS/Azure EBS/Disk Encryption Setting using a Customer Managed Encryption Key (CMEK)**
The EBS/Disk Encryption Gateway setting can use a Customer Managed Encryption Key (CMEK).  The key specified is created in specific locations related to each CSP.  For AWS, the key is created in the Key Management System (KMS) and referenced by its ID.  For Azure, the key is created in the Disk Encryption Sets and referenced by its full path.  The Azure Access control (IAM) Role for the Multicloud Defense Controller (valtix-controller-role) requires an additional permission: `Microsoft.Compute/sshPublicKeys/read`.  This permission has been updated in the Azure PowerShell script used to prepare the Subscription for onboarding into Multicloud Defense.

### Gateway Assign Public IP Setting

#### To deploy Gateway instances with no public IPs assigned to the interfaces
```hcl
settings {
  name = "controller.gateway.assign_public_ip"
  value = false
}
```

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
When a Gateway resource is created, if an instance creation fails, the Controller will initiate a retry.  If the issue is corrected, then the Gateway will eventually be successfully created. If the issue is not corrected, then the Controller will retry indefinitely.  If the Controller continues to retry without success, Terraform will timeout after 15 minutes and produce an error stating the Gateway is not yet ACTIVE.

The Gateway Instance Creation Retry Settings provide a user with some control over the instance creation retry behavior.  The settings are defined and behave as follows:
* `controller.gateway.instance_creation_retry_count` - Defines the number of times the Controller will retry creating each instance.  If the retry count is exceeded, then the Controller will no longer retry the creation and the Gateway will remain in ACTIVE_PENDING state.  The Controller will reinitiate its retry attempts once the retry reset time has expired.  If this setting is not specified, the Controller will retry indefinitely.
* `controller.gateway.instance_creation_retry_reset_time` - Defines the amount of time (in minutes) after the retry count has been exceeded that the Controller will reinitiate its retry attempts. If this setting is not specified, the reset time will be infinite and the Controller will never reinitiate its retry attempts. This setting is only applicable if the retry count setting is specifed.

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
* `ftdv_version` - Image version of the Gateway
* `ftdv_license_model` - Licensing models applied to the Gateway
* `ftdv_performance_tier` - Performance tier applied to the Gateway 
* `ftdv_policy_id` - ID of the policy applied to the Gateway
* `gateway_endpoint` - (Azure only) The NLB endpoint (IP Address) to be used as a target for routing traffic to the Gateway
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

## Import
FTDv Gateway resources can be imported using the resource `name`:

```hcl
$ terraform import valtix_ftdv_gateway.aws-ftdv-gw1 aws-ftdv-gw1
```