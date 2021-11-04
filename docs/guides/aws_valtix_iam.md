# Valtix IAM 
In order for Valtix to inventory cloud resources and deploy Valtix gateways, IAM roles need to be created in the AWS account.  Valtix has a Cloud Formation template to create these roles.  This Terraform module performs the same function for customers to create the IAM roles required to deploy Valtix gateways. This is useful in order to automate the deployment of roles across a large number of AWS accounts. 

Please contact a Valtix solutions architect to review the IAM scopes to ensure least privilege in production deployments

The module can be found [here](https://github.com/valtix-security/terraform-aws-setup)
