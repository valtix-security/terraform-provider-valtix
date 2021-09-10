# Overview

Valtix provider is used to interact with Valtix resources like address objects,
service objects, gateways using Terraform.

Use the navigation on the left to read about each of the available resources.

## Authentication using Valtix API Key

To create Valtix resources using provider you need to authenticate with the Valtix controller using an API key generated on the Valtix controller. The key can be generated only by the super-admin user and the permissions for the key can be assigned when generating the key

## Generate Key

1. Login to the Valtix controller with your user name and password
1. Navigate to **Settings** and click on the **API Keys** in the side bar
1. Click **Create API Key** button
1. Provide a description for the key
1. If you are a super-admin or admin you can assign the permissions for the key. For terraform use, select **admin_rw** as it is used to create resources
1. Click **Save**
1. Once the key is successfully created, click **Download** to download the key file.
1. *NOTE: API Key and Secret are not used for terraform*
1. Use the downloaded key file while initializing the Valtix provider

## Using Key

```hcl
terraform {
  required_providers {
    valtix = {
      source = "valtix-security/valtix"
      # version = "2.5.0"
    }
  }
}

provider "valtix" {
  api_key_file = file(var.valtix_api_key_file)
}
```

Contact Valtix at support@valtix.com to get information about the service, port and acctname assigned to you.
