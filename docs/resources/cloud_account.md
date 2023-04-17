# Resource: valtix_cloud_account
Resource for creating and managing Valtix Cloud Accounts consisting of on-boarded AWS Accounts, Azure Subscriptions, GCP Projects and OCI Tenants.  The Valtix Cloud Account defines the credentials used for the Valtix Controller to discover inventory assets and traffic, and orchestrate and manage the deployment of Valtix Gateways.

## Example Usage

## AWS
Create a cross account IAM role before running this block. Look at the [AWS Cloud Provider Setup](https://docs.valtix.com/userguide/setup_csp/aws/overview/) for more details. Use a Valtix generated External ID in the trust Policy of the IAM role, and the same External ID in the AWS Cloud Account Setup.

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
Create an application and secret before running this block. Look at the [Azure Cloud Provider Setup](https://docs.valtix.com/userguide/setup_csp/azure/overview/) for more details.

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
Create a GCP Service Account for use by the Valtix controller and generate/download the key file before running this block. Look at the [GCP Cloud Provider Setup](https://docs.valtix.com/userguide/setup_csp/gcp/overview/) for more details.

```hcl
resource "valtix_cloud_account" "gcp_account1" {
  name                 = "gcp-account1"
  description          = "GCP Project - Production"
  csp_type             = "GCP"
  gcp_credentials_file = file("valtix_controller_key.json")
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
* `name` - (Required) Name of the Cloud Account on the Valtix Console. Must contain only alphanumeric, hyphens or underscore characters and not exceed 100 characters
* `description` - (Optional) Description of the Cloud Account
* `csp_type` - (Required)  Defines the Cloud Service Provider. Must be `GCP`, `AWS` or `AZURE`

### AWS Arguments
* `aws_credentials_type` - (AWS - Required) must be `AWS_IAM_ROLE`
* `aws_iam_role` - (AWS - Required) IAM role ARN used to deploy and manage Valtix in your cloud account
* `aws_account_number` - (AWS - Required) AWS account number
* `aws_iam_role_external_id` - (AWS - Required) External Id for trust relationship
* `aws_inventory_iam_role` - (AWS - Optional) IAM Role ARN used by CloudWatch Event Rule to post inventory events to the Valtix Controller
* `inventory_monitoring` - Enable inventory monitoring (can be repeated multiple times).  See [Inventory Monitoring](#inventory-monitoring) for details.

### Azure Arguments
* `azure_directory_id` - (Azure - Required) Azure Active Directory Id (Tenant Id)
* `azure_subscription_id` - (Azure - Required) Azure Subscription Id where the Valtix gateway instances are deployed
* `azure_application_id` - (Azure - Required) Azure Application Id that's used as credentials (along with the secret) to manage Azure account/subscription
* `azure_client_secret` - (Azure - Required) Azure client secret for the above application
* `inventory_monitoring` - Enable inventory monitoring (can be repeated multiple times).  See [Inventory Monitoring](#inventory-monitoring) for details.

### GCP Arguments
* `gcp_credentials_file` - (GCP - Required) Service Account credentials key file created for the Valtix Controller access.
* `inventory_monitoring` - Enable inventory monitoring (can be repeated multiple times). See [Inventory Monitoring](#inventory-monitoring) for details.
* `gcp_project_id` - (Optional) Add the specified Project ID (name) to the Valtix Controller. If this is not specified, then the Project ID from the credentials file is used. The argument is required if a Service Account is used to manage multiple GCP Projects.

## Inventory Monitoring
* `regions` - List of regions to enable and monitor inventory
* `refresh_interval` - Interval in minutes where the inventory is refreshed

## Attribute Reference
* `id` - ID of the Cloud Account resource that can be referenced in other resources

## Import
Cloud Account resources can be imported using the resource `name`:

```hcl
$ terraform import valtix_cloud_account.aws1 aws-account1
```