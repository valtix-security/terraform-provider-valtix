# Overview

Valtix terraform provider can be used to create all the valtix resources like address objects,
service objects, gateways etc using terraform instead of navigating the UI (valtix dashboard). This helps
in managing the Valtix infrastructure as code.

The provider currently works for the most commonly used resources. For the cloud account and gateways
it currently works only for GCP. AWS and Azure is coming soon and this page will be updated once
the provider is ready for AWS and Azure.

Here is a sample configuration that creates the following resources:

* Add GCP cloud project to the Valtix controller
* Add an address object that represents a backend application which will be protected (proxied) by Valtix gateway
* Add a service object to proxy the above address object by the Valtix GW
* Add a ruleset and a rule to allow traffic to the application from all sources on the internet
* Add a Valtix Gateway and assign the rule set to this gateway.

Most of the values are defined as variables. You can customize it further if required.

## variables.tf

```
# Ask valtix representative for this info
variable "valtix_acct_name" {
  description = "Valtix account name created for the customer"
}

# Ask valtix representative for this info
variable "server" {
  description = "Valtix SaaS controller endpoint"
  default = "prod1-apiserver.vtxsecurityservices.com"
}

# Ask valtix representative for this info
variable "port" {
  description = "Valtix SaaS controller endpoint port"
  default     = "8091"
}

# Ask valtix representative for this info. This will be a self-service option soon
variable "valtix_api_key_file" {
  description = "API Key file name for authenticating with Valtix SaaS controller"
}

# Create a service account and generate a key file and provide that file name with
# full path for this value.
variable "gcp_key_file" {
  description = "Key file name for authenticating with GCP"
}

variable "gcp_project_name" {
  description = "GCP project name"
}

variable "gcp_gw_service_account_email" {
  description = "Service account email for GCP Gateway"
}

variable "gcp_region" {
  description = "GCP region in which the Valtix gateway is created"
  default = "us-east1"
}

# This is the name shown on the Valtix dashboard that represents the GCP account.
# You can use the same name as your GCP project.
variable "csp_account_name" {
  description = "CSP Account name added to the valtix controller"
  default = "gcpaccount_name"
}

# The full name looks like
# https://www.googleapis.com/compute/v1/projects/gcp-project/global/networks/datapath-vpc
# In this example the data source is used to get this full name from the display name.
# If you don't want to use the data source, then provide this full name in the valtix_gateway
# resource
variable "mgmt_vpc" {
  description = "Management VPC Network name"
}

# The full name looks like
# https://www.googleapis.com/compute/v1/projects/gcp-project/global/networks/datapath-vpc
# In this example the data source is used to get this full name from the display name.
# If you don't want to use the data source, then provide this full name in the valtix_gateway
# resource
variable "datapath_vpc" {
  description = "VPC Network name where the apps run (datapath)"
}

# This tag is assigned to the management network interface. The GCP firewalls
# must be configured to allow outgoing traffic from the tag.
# (by default its allowed, so no configuration is required unless your org prevents that)
variable "mgmt_network_tag" {
  description = "Management network tag"
  default = "valtix-mgmt"
}

# This tag is assigned to the datapath network interface. The GCP firewalls
# must be configured to allow incoming traffic to the tag
# For every service that's configured on Valtix the corresponding listener port
# on the service must be opened in this GCP firewall.
variable "datapath_network_tag" {
  description = "Datapath network tag"
  default = "valtix-datapath"
}

variable "gcp_zone1" {
  description = "GCP zone in which the Valtix gateway is created"
  default = "us-east1-b"
}

# The full name looks like
# https://www.googleapis.com/compute/v1/projects/gcp-project/regions/us-west1/subnetworks/mgmt-subnet
# In this example the data source is used to get this full name from the display name.
# If you don't want to use the data source, then provide this full name in the valtix_gateway
# resource
variable "mgmt_subnet1" {
  description = "Management subnet name in mgmt_vpc / gcp_zone1"
}

# The full name looks like
# https://www.googleapis.com/compute/v1/projects/gcp-project/regions/us-west1/subnetworks/mgmt-subnet
# In this example the data source is used to get this full name from the display name.
# If you don't want to use the data source, then provide this full name in the valtix_gateway
# resource
variable "datapath_subnet1" {
  description = "Datapath subnet name in datapath_vpc / gcp_zone1. Similar to mgmt_subnet1"
}
```

## values

```
valtix_acct_name = "cutomer-account1"
server = "prod-apiserver.vtxsecurityservices.com"
port = "8091"
valtix_api_key_file = "my-valtix-auth.json"
gcp_key_file = "gcp-key-file.json"
gcp_project_name = "gcp-project1"
gcp_gw_service_account_email = "valtix-gw@gcp-project1.iam.gserviceaccount.com"
gcp_region = "us-east1"
csp_account_name = "gcp-account-gcp-project1"
mgmt_vpc = "valtix-mgmt"
datapath_vpc = "valtix-datapath"
mgmt_network_tag = "valtix-mgmt"
datapath_network_tag = "valtix-datapath"
gcp_zone1 = "us-east1-b"
mgmt_subnet1 = "valtix-mgmt1"
datapath_subnet1 = "valtix-subnet1"
```

## main.tf

This uses the data source for google to query the vpc and subnet names as defined in the values

```
provider "google" {
  credentials = file(var.gcp_key_file)
  project     = var.gcp_project_name
  region      = var.gcp_region
}

data "google_compute_network" "mgmt_vpc" {
  name = var.mgmt_vpc
}

data "google_compute_network" "datapath_vpc" {
  name = var.datapath_vpc
}

data "google_compute_subnetwork" "mgmt_subnet1" {
  name   = var.mgmt_subnet1
  region = var.gcp_region
}

data "google_compute_subnetwork" "datapath_subnet1" {
  name   = var.datapath_subnet1
  region = var.gcp_region
}

provider "valtix" {
  acctname     = var.valtix_acct_name
  server       = var.server
  port         = var.port
  api_key_file = file(var.valtix_api_key_file)
}

resource "valtix_cloud_account" gcp1 {
  name             = var.csp_account_name
  csp_type         = "GCP"
  gcp_credentials_file     = file(var.gcp_key_file)
}

resource "valtix_certificate" self_signed_cert_1 {
    name = "self_signed_cert_1"
    certificate_type = "IMPORT_CONTENTS"
    # this is your own certificate and private key that will be presented to the user
    # when they access the backend application via valtix gateway (proxy)
    certificate_body = file("cert.pem")
    private_key = file("key.pem")
}

resource "valtix_profile_decryption" decryption_profile_1 {
    name = "tls_self_signed_1"
    certificate_name = "${valtix_certificate.self_signed_cert_1.name}"
}

resource "valtix_address_object" app1-ag {
  name = "app1"
  description = "Backend App"
  type = "STATIC"
  value = ["10.10.10.10"]
  backend_address = true
}

resource "valtix_service_object" app1-svc-http {
  name = "app1-svc-http"
  description = "app1 service: listen on 80 and target to 80"
  service_type = "ReverseProxy"
  transport_mode = "HTTP"
  port {
    destination_ports = "80"
    backend_ports = "80"
  }
  backend_address_group = "${valtix_address_object.app1-ag.address_id}"
}

resource "valtix_service_object" app1-svc-https {
  name = "app1-svc-https"
  description = "app1 service: listen on 443 and target to 443"
  service_type = "ReverseProxy"
  transport_mode = "HTTPS"
  port {
    destination_ports = "443"
    backend_ports = "443"
  }
  backend_address_group = "${valtix_address_object.app1-ag.address_id}"
  tls_profile = valtix_profile_decryption.decryption_profile_1.profile_id
}

resource "valtix_policy_rule_set" ingress_policy_rule_set {
  name        = "ingress_rule_set"
  rule {
    name = "rule1"
    type = "ReverseProxy"
    action = "ALLOW_LOG"
    service = "${valtix_service_object.app1-svc-http.service_id}"
    description = "listen port 80 to target port 80 on app1"
  }
  rule {
    name = "rule2"
    type = "ReverseProxy"
    action = "ALLOW_LOG"
    service = "${valtix_service_object.app1-svc-https.service_id}"
    description = "listen port 443 to target port 443 on app1"
  }
}

resource "valtix_gateway" gcp-gw1 {
  name                      = "gcp-gw1"
  description               = "GCP 1st gateway"
  csp_account_name          = "${valtix_cloud_account.gcp1.name}"
  instance_type             = "GCP_E2_8"
  gateway_image             = "2.3-01"
  deployment_type           = "EDGE"
  policy_rule_set_id        = "${valtix_policy_rule_set.ingress_policy_rule_set.rule_set_id}"
  gcp_service_account_email = var.gcp_gw_service_account_email
  region                    = var.gcp_region
  vpc_id                    = data.google_compute_network.datapath_vpc.self_link
  mgmt_vpc_id               = data.google_compute_network.mgmt_vpc.self_link
  mgmt_security_group       = var.mgmt_network_tag
  datapath_security_group   = var.datapath_network_tag
  instance_details {
    availability_zone = var.gcp_zone1
    mgmt_subnet       = data.google_compute_subnetwork.mgmt_subnet1.self_link
    datapath_subnet   = data.google_compute_subnetwork.datapath_subnet1.self_link
  }
  /*
  repeat instance_details block to create instances in other zones.
  create a data for getting subnet names or provide the full subnet names
  */
}
```
