# Resource: valtix_cloud_account
Resource for creating and managing Valtix Cloud Accounts consisting of on-boarded AWS Accounts, Azure Subscriptions, GCP Projects and OCI Tenants.  The Valtix Cloud Account defines the credentials used for the Valtix Controller to discover inventory assets and traffic, and orchestrate and manage the deployment of Valtix Gateways.

## Example Usage

## AWS
Create a cross account IAM role before running this block. Please see [AWS Cloud Provider Setup](https://docs.valtix.com/userguide/setup_csp/aws/overview/) for more details. Use a Valtix generated External ID in the Policy of the IAM Role, and the same External ID in the AWS Cloud Account resource.

```hcl
resource "valtix_cloud_account" "aws1" {
  name                     = "aws-account1"
  description              = "AWS Account - 123456789012"
  csp_type                 = "AWS"
  aws_credentials_type     = "AWS_IAM_ROLE"
  aws_iam_role             = "arn:aws:iam::123456789012:role/valtix-controller-role"
  aws_account_number       = "123456789012"
  aws_iam_role_external_id = var.external_id
  aws_inventory_iam_role   = "arn:aws:iam::123456789012:role/valtix-inventory-role"
}
```

## Azure
Create an application and secret before running this block. Please see [Azure Cloud Provider Setup](https://docs.valtix.com/userguide/setup_csp/azure/overview/) for more details.

```hcl
resource "valtix_cloud_account" "azure_account1" {
  name                  = "azure-account1"
  description           = "Azure Subscription - 11111111-2222-4bab-aaec-ae38a24dd990"
  csp_type              = "AZURE"
  azure_directory_id    = "11111111-2222-4bab-aaec-ae38a24dd990"
  azure_subscription_id = "11111111-2222-41a6-8dfc-05b1fc703aa7"
  azure_application_id  = "11111111-2222-499b-a03f-e47e012e63ae"
  azure_client_secret   = "client-secret-password"
}
```

## GCP
Create a GCP Service Account for use by the Valtix Controller and generate/download the key file before running this block. Please see [GCP Cloud Provider Setup](https://docs.valtix.com/userguide/setup_csp/gcp/overview/) for more details.

```hcl
resource "valtix_cloud_account" "gcp_account1" {
  name                 = "gcp-account1"
  description          = "GCP Project - Production"
  csp_type             = "GCP"
  gcp_credentials_file = file("valtix_controller_key.json")
}
```

```hcl
resource "valtix_cloud_account" "gcp_folder1" {
  name                 = "gcp-folder1"
  description          = "GCP Folder - Production"
  csp_type             = "GCP"
  gcp_folder_id        = <Folder ID>
  gcp_credentials_file = file("valtix_controller_key.json")
  inventory_monitoring {
    regions          = ["us-central", "us-west"]
    refresh_interval = 60
  }
}
```

GCP Folders can be onboarded in lieu of onboarding individual GCP Projects.  Consider the following as intended restrictions:
* Folders that do not have the `roles/compute.admin` permission enabled are considered empty and will not be used
* Projects associated with onboarded Folders will be used for asset and traffic discovery only
* Projects associated with onboarded Folders will not accommodate orchestrating Service VPC or Gateway creation

## OCI
Create a policy, user, group and secret before running this block. Please see [OCI Cloud Provider Setup](https://docs.valtix.com/userguide/setup_csp/oci/overview/) for more details.

```hcl
resource "valtix_cloud_account" "oci_account1" {
  name                 = "oci-account1"
  description          = "OCI Tenant - Production"
  csp_type             = "OCI"
  oci_tenant_id        = "ocid1.tenancy.oc1..aaaaaaaa76nyd44x2kruauq47wqngjm7m51zapquxo7bqox6vy9681uz47oq"
  oci_compartment_id   = "ocid1.compartment.oc1..aaaaaaaauo3cpjxzlgebu3zmzsirphib4mtpzgxa4ca2rbzhlbwokpzrjixa"
  oci_user_id          = "ocid1.user.oc1..aaaaaaaa1jqpgy3vqs9ysrmx7rmi51bqokqj2ek0iphhbxyurgoofzh9ymma"
  oci_client_secret    = <Client Secret>
}
```

## Account with Inventory Monitoring
```hcl
resource "valtix_cloud_account" "aws_account1" {
  name                     = "aws-account1"
  csp_type                 = "AWS"
  aws_credentials_type     = "AWS_IAM_ROLE"
  aws_iam_role             = "arn:aws:iam::123456789012:role/valtix-controller-role"
  aws_account_number       = "123456789012"
  aws_iam_role_external_id = "shared-external-id"
  inventory_monitoring {
    regions          = ["us-east-1", "us-east-2"]
    refresh_interval = 10
  }
  inventory_monitoring {
    regions          = ["us-west-1", "us-west-2"]
    refresh_interval = 30
  }
}
```

## Argument Reference
* `name` - (Required) Name of the Cloud Account that will be used as reference in other Valtix resources. Must contain only alphanumeric, hyphens or underscore characters and must not exceed 100 characters.
* `description` - (Optional) Description of the Cloud Account
* `csp_type` - (Required) Cloud Service Provider (CSP) to use for onboarding. Applicable values: `GCP`, `AWS`, `AZURE`, `OCI`.

### AWS Arguments
* `aws_credentials_type` - (Required) Must be specified as `AWS_IAM_ROLE`
* `aws_iam_role` - (AWS - Required) IAM role ARN used to deploy and manage Valtix in your cloud account
* `aws_account_number` - (Required) AWS Account Number to onboard
* `aws_iam_role_external_id` - (Required) External ID to use as the trust relationship between the Controller and the AWS Account
* `aws_inventory_iam_role` - (Optional) IAM Role ARN used by the CloudWatch Event Rule to post inventory events to the Controller for real-time inventory discovery
* `inventory_monitoring` - (Optional) Region-based inventory monitoring block. This block can be repeated multiple times. See [Inventory Monitoring](#inventory-monitoring) for the block structure.

### Azure Arguments
* `azure_directory_id` - (Required) Azure Active Directory ID (Tenant ID)
* `azure_subscription_id` - (Required) Azure Subscription ID to onboard
* `azure_application_id` - (Required) Azure Application ID to use as the trust relationship between the Controller and the Azure Subscription
* `azure_client_secret` - (Required) Azure Client Secret to use as the trust relationship between the Controller and the Azure Application
* `inventory_monitoring` - (Optional) Region-based inventory monitoring block. This block can be repeated multiple times. See [Inventory Monitoring](#inventory-monitoring) for the block structure.

### GCP Arguments
* `gcp_project_id` - (Optional) GCP Project ID to onboard. If not specified, the Project ID is obtained from the credentials file. This argument is necessary if the same Service Account is used to onboard multiple Projects. This argument is not used if the Folder ID is onboarded using the `gcp_folder_id` argument. Regardless of whether used or not, the Project ID field will be populated from either a specified Project ID using this argument or by obtaining the Project ID from the credentials file.
* `gcp_folder_id` - (Optional) GCP Folder ID to onboard. This argument is used to simplify the onboarding of multiple Projects that are contained within a Folder hierarchy.  The Projects onboarded as part of a Folder ID hierarchy cannot be used for Service VPC and Gateway orchestration.  They can only be used for asset and traffic discovery. 
* `gcp_credentials_file` - (Required) Service Account credentials file to use as the trust relationship between the Controller and the GCP Project/Folder
* `inventory_monitoring` - (Optional) Region-based inventory monitoring block. This block can be repeated multiple times. See [Inventory Monitoring](#inventory-monitoring) for the block structure.

### OCI Arguments
* `oci_tenant_id` - (Required) OCI Tenant ID to onboard
* `oci_compartment_id` - (Required) OCI Compartment ID where the Gateways will be deployed
* `oci_user_id` - (Required) OCI User ID use to define the permissions that are granted to the Controller
* `oci_client_secret` - (Required) OCI Client Secret to use as the trust relationship between the Controller and the OCI Tenant

## Inventory Monitoring
* `regions` - List of Regions to enable periodic API-based periodic refresh and push-based real-time inventory monitoring
* `refresh_interval` - Interval (in minutes) to use for periodic API-based inventory monitoring

## Attribute Reference
* `id` - ID of the Cloud Account resource that can be referenced in other resources

## Import
Cloud Account resources can be imported using the resource `name`:

```hcl
$ terraform import valtix_cloud_account.aws1 aws-account1
```