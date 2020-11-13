# API Key
To create Valtix resources using terraform provider you need to authenticate with the controller. Terraform provider does not use username and password that you use to login to the controller.

For this purposes you need to generate an API key. This is similar to AWS access key and secret or Azure application secret.
The key can be generated only by the super-admin user and the permissions for the key can be assigned when generating the key.

## Generate Key

1. Login to the Valtix controller with your user name and password
1. Navigate to **Settings** and click on the **API Keys** in the side bar
1. Click **Create API Key** button
1. Provide a description for the key
1. If you are a super-admin or admin you can assign the permissions for the key. For terraform use, select **admin_rw** as it is used to create resources
1. Click **Save**
1. Once the key is successfully created, click **Download** to download the key file.
1. *NOTE: API Key and Secret are not used for terraform*
1. Use the downloaded key file while initializing the valtix terrraform provider

## Using Key

```hcl
provider "valtix" {
  acctname     = "valtix-provided-tenant-name"
  server       = "server-url"
  port         = "port-number"
  api_key_file = file(var.valtix_api_key_file)
}
```

Talk to Valtix to get information about the servicer, port and acctname assigned to you.