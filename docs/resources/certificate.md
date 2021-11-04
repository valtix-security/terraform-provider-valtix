# Resource: valtix_certificate
Manages certificates that are presented to the end user when they access valtix gw proxy

## Example Usage

### Import a certificate and key that's on your local file system
```hcl
resource "valtix_certificate" cert1 {
  name              = "self_signed_cert_1"
  certificate_type  = "IMPORT_CONTENTS"
  certificate_body  = file("cert.pem")
  private_key       = file("key.pem")
  // body and key values can also be the strings
}
```

### Import a certificate from AWS Secrets Manager
```hcl
resource "valtix_certificate" "cert1" {
  name             = "aws_secret"
  certificate_type = "AWS_SECRET"
  certificate_body = file("cert.pem")
  csp_account_name = "csp_account_name_added_to_valtix_controller"
  region           = "us-east-1"
  aws_secret_name  = "my_key1"
}
```

### Import a certificate from AWS KMS
```hcl
resource "valtix_certificate" "kms" {
  name                    = "aws_kms"
  certificate_type        = "AWS_KMS"
  certificate_body        = file("cert.pem")
  csp_account_name        = "csp_account_name_added_to_valtix_controller"
  region                  = "us-east-1"
  private_key_cipher_text = file("key.pem")
}
```

### Import a certificate from Azure Key Vault and Secret
```hcl
resource "valtix_certificate" "azure" {
  name                        = "azure"
  certificate_type            = "AZURE_KEY_VAULT_SECRET"
  certificate_body            = file("cert.pem")
  csp_account_name            = "csp_account_name_added_to_valtix_controller"
  region                      = "eastus"
  azure_key_vault_name        = "vault1"
  azure_key_vault_secret_name = "secret1"
}
```

### Import a certificate from GCP Secrets Manager
```hcl
resource "valtix_certificate" "cert1" {
  name             = "self_signed_cert_1"
  certificate_type = "GCP_SECRET"
  certificate_body = file("cert.pem")
  csp_account_name = "csp_account_name_added_to_valtix_controller"
  secret_name      = "my_key1"
  secret_version   = "12"
}
```

## Argument Reference
* `name` - (Required) Name of the certificate
* `description` - Description of the certificate
* `certificate_type` - (Required) "IMPORT_CONTENTS", "AWS_KMS", "AWS_SECRET", "AZURE_KEY_VAULT_SECRET", "GCP_SECRET"
* `certificate_body` - (Required) Certificate content. Provide a string with the whole content here or if it's on a file system use `file("filename")`
* `certificate_chain` - (Optional) Certificate chain content. Provide a string with the whole content here or if it's on a file system use `file("filename")`. This is optional if the certificate has a chain available separately and not part of the certificate_body

## Additional arguments based on the certificate_type

### IMPORT_CONTENTS
* `private_key` - (Required for IMPORT_CONTENTS) Certificate content. Provide a string with the whole content here or if it's on a file system use `file("filename")`

### AWS_KMS
* `csp_account_name` - (Required) To use a private key that's stored on GCP secrets manager, provide the csp_account_name (GCP) that stores the secrets key.
* `region` - (Required) Region where the key/secret is location
* `private_key_cipher_text` - (Required) KMS encrypted cipher text

### AWS_SECRET
* `csp_account_name` - (Required) To use a private key that's stored on GCP secrets manager, provide the csp_account_name (GCP) that stores the secrets key
* `region` - (Required) Region where the key/secret is location
* `aws_secret_name` - (Required) Secrets manager key name that has the private key

### GCP_SECRET
* `csp_account_name` - (Required) To use a private key that's stored on GCP secrets manager, provide the csp_account_name (GCP) that stores the secrets key
* `secret_name` - (Required) Secrets manager key name that has the private key
* `secret_version` - (Optional) Secrets manager key version

### AZURE_KEY_VAULT_SECRET
* `csp_account_name` - (Required) To use a private key that's stored on GCP secrets manager, provide the csp_account_name (GCP) that stores the secrets key
* `region` - (Required) Region where the key/secret is location
* `azure_key_vault_name` - (Required) Azure key vault name
* `azure_key_vault_secret_name` - (Required) Azure secret name

## Attribute Reference
* `name` - Name of the certificate that can be referenced in other resources (e.g. valtix_profile_decryption)