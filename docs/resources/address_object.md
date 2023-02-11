# Resource: valtix_address_object
Resource for creating and managing Address Objects that can be used as a Source or Destination (Src/Dest) in a Policy Ruleset Rule, or as a Target Backend Address (Reverse Proxy Target) in a Reverse Proxy Service Object.

The Address Object is used in a Policy Ruleset Rule to define the segmentation policy.

## Example Usage

### STATIC (Source Destination) Examples
```hcl
resource "valtix_address_object" "ip_cidr_ag" {
  name        = "ip_cidr"
  description = "Static IP/CIDR"
  type        = "STATIC"
  value       = ["10.0.0.0/16", "192.168.0.0/16", "172.16.1.15"]
}
```

```hcl
resource "valtix_address_object" "fqdn_ag" {
  name        = "fqdn"
  description = "Static FQDN"
  type        = "STATIC"
  value       = ["fqdn1.example.com", "fqdn2.example.com", "fqdn3.example.com"]
}
```
For a complete set of arguments, see [STATIC (Source Destination) Arguments](#static-source-destination-arguments)


### STATIC (Reverse Proxy Target) Examples
```hcl
resource "valtix_address_object" "app1_ag" {
  name            = "app1"
  description     = "Static IP (backend)"
  type            = "STATIC"
  value           = ["10.10.10.10"]
  backend_address = true
}
```

```hcl
resource "valtix_address_object" "app2_ag" {
  name            = "app2"
  description     = "Static FQDN (backend)"
  type            = "STATIC"
  value           = ["app2.example.com"]
  backend_address = true
}
```
For a complete set of arguments, see [STATIC (Reverse Proxy Target) Arguments](#static-reverse-proxy-target-arguments)

### DYNAMIC_APPLICATIONS (Reverse Proxy Target) Example
```hcl
resource "valtix_address_object" "app3_ag" {
    name        = "app3"
    description = "Dynamic Applications (backend)"
    type        = "DYNAMIC_APPLICATIONS"
    tag_list {
        tag_key   = "tag-key1"
        tag_value = "tag-value1"
    }
}
```
For a complete set of arguments, see [DYNAMIC_APPLICATIONS (Reverse Proxy Target) Arguments](#dynamic_applications-reverse-proxy-target-arguments)


### DYNAMIC_VPC (Source Destination) Example
```hcl
resource "valtix_address_object" "vpc1_ag" {
    name             = "vpc1"
    description      = "Dynamic VPC"
    type             = "DYNAMIC_VPC"
    csp_account_name = valtix_cloud_account.aws1.name
    region           = var.aws_region
    vpc_id           = data.aws_vpc.vpc1.id
}
```
For a complete set of arguments, see [DYNAMIC_VPC (Source Destination) Arguments](#dynamic_vpc-source-destination-arguments)

### DYNAMIC_INSTANCE (Source Destination) Example
```hcl
resource "valtix_address_object" "instance1_ag" {
    name             = "instance1"
    description      = "Dynamic Instance"
    type             = "DYNAMIC_INSTANCE"
    csp_account_name = valtix_cloud_account.aws1.name
    region           = var.aws_region
    vpc_id           = data.aws_vpc.vpc1.id
    instance_id      = data.aws_instance.instance1.id
}
```
For a complete set of arguments, see [DYNAMIC_INSTANCE (Source Destination) Arguments](#dynamic_instance-source-destination-arguments)

### DYNAMIC_SUBNET (Source Destination) Example
```hcl
resource "valtix_address_object" "subnet1_ag" {
    name             = "subnet1"
    description      = "Dynamic Subnet"
    type             = "DYNAMIC_SUBNET"
    csp_account_name = valtix_cloud_account.aws1.name
    region           = var.aws_region
    vpc_id           = data.aws_vpc.vpc1.id
    subnet_id        = data.aws_subnet.subnet1.id
}
```
For a complete set of arguments, see [DYNAMIC_SUBNET (Source Destination) Arguments](#dynamic_subnet-source-destination-arguments)

### DYNAMIC_SECURITY_GROUP (Source Destination) Example
```hcl
resource "valtix_address_object" "securitygroup1_ag" {
    name              = "securitygroup1"
    description       = "Dynamic Security Group"
    type              = "DYNAMIC_SECURITY_GROUP"
    csp_account_name  = valtix_cloud_account.aws1.name
    region            = var.aws_region
    vpc_id            = data.aws_vpc.vpc1.id
    security_group_id = data.aws_security_group.sg1.id
}
```
For a complete set of arguments, see [DYNAMIC_SECURITY_GROUP (Source Destination) Arguments](#dynamic_security_group-source-destination-arguments)

### DYNAMIC_USER_DEFINED_TAG (Source Destination) Example
```hcl
resource "valtix_address_object" "udt1_ag" {
    name        = "udt1"
    description = "Dynamic User Defined Tag"
    type        = "DYNAMIC_USER_DEFINED_TAG"
    tag_list {
        tag_key       = "tag-key1"
        tag_value     = "tag-value1"
        resource_type = "RESOURCE_SUBNET"
    }
    tag_list {
        tag_key       = "tag-key2"
        tag_value     = "tag-value2"
        resource_type = "RESOURCE_VPC"
    }
}
```
For a complete set of arguments, see [DYNAMIC_USER_DEFINED_TAG (Source Destination) Arguments](#dynamic_user_defined_tag-source-destination-arguments)

### GEO_IP (Source Destination) Example
```hcl
resource "valtix_address_object" "geoip1_ag" {
  name        = "geoip1"
  description = "GeoIP Country"
  type        = "GEO_IP"
  value       = ["Country1", "Country2", "Country3"]
  match_xff   = true
}
```
For a complete set of arguments, see [GEO_IP (Source Destination) Arguments](#geo_ip-source-destination-arguments)

### GROUP (Source Destination) Example
```hcl
resource "valtix_address_object" "group1_ag" {
  name              = "group1"
  description       = "Address Object Group"
  type              = "GROUP"
  address_group_ids = [valtix_address_object.addr1.id, valtix_address_object.addr2.id]
}
```
For a complete set of arguments, see [GROUP (Source Destination) Arguments](#group-source-destination-arguments)

### DYNAMIC_ASG (Source Destination) Example
```hcl
resource "valtix_address_object" "azure_asg_ag" {
  name                          = "azure_asg"
  description                   = "Azure Application Security Group"
  type                          = "DYNAMIC_ASG"
  csp_account_name              = valtix_cloud_account.azure.name
  region                        = var.location
  resource_group                = var.resource_group
  application_security_group_id = var.asg_id
}
```
For a complete set of arguments, see [DYNAMIC_ASG (Source Destination) Arguments](#dynamic_asg-source-destination-arguments)

## Argument Reference

#### STATIC (Source Destination) Arguments
* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` = `STATIC` - (Required) Type of the Address Object
* `value` - (Required) A list of IPs, CIDRs or FQDNs. The total number of FQDNs is limited to 200 where each FQDN can resolve to at most 400 IPs.
<br><br>For an example, see [STATIC (Source Destination) Example](#static-source-destination-examples)

#### STATIC (Reverse Proxy Target) Arguments

* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` = `STATIC` - (Required) Type of the Address Object
* `value` - (Required) A list of one or more IPs/FQDNs
* `backend_address` - (Required) This argument must be set to `true`
<br><br>For an example, see [STATIC (Reverse Proxy Target) Example](#static-reverse-proxy-target-examples)

#### DYNAMIC_APPLICATIONS (Reverse Proxy Target) Arguments
* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` = `DYNAMIC_APPLICATIONS` - (Required) Type of the Address Object
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the `tag_list`
* `vpc_id` - (Optional) The VPC/VNet ID to restrict the scope of the `tag_list`
* `region` - (Optional) The Region to restrict the scope of the `tag_list`
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the `tag_list`
* `tag_list` - (Required) A single block that represents a Tag key-value pair associated with one or more Application Load Balancers. `tag_list` structure [defined below](#tag-list). The `tag_list` is used to dynamically associate the IPs or FQDNs of the set of Application Load Balancers that match the Tag key-value pair.  
* `backend_address` - (Required) This argument must be set to `true`
<br><br>For an example, see [DYNAMIC_APPLICATIONS (Reverse Proxy Target) Example](#dynamic_applications-reverse-proxy-target-example)

#### DYNAMIC_VPC (Source Destination) Arguments
* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` = `DYNAMIC_VPC` - (Required) Type of the Address Object
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the `vpc_id`
* `region` - (Required) The Region to restrict the scope of the `vpc_id`
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the `vpc_id`
* `vpc_id` - (Required) The VPC/VNet ID is used to dynamically associate all CIDRs of the VPC/VNet
<br><br>For an example, see [DYNAMIC_VPC (Source Destination) Example](#dynamic_vpc-source-destination-example)

#### DYNAMIC_SECURITY_GROUP (Source Destination) Arguments
* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` = `DYNAMIC_SECURITY_GROUP` - (Required) Type of the Address Object
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the `security_group_id`
* `region` - (Required) The Region to restrict the scope of the `security_group_id`
* `vpc_id` - (Required) The VPC/VNet ID to restrict the scope of the `security_group_id`
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the `security_group_id`
* `security_group_id` - (Required) The Security Group ID used to dynamically associate all IPs of the Security Group
<br><br>For an example, see [DYNAMIC_SECURITY_GROUP (Source Destination) Example](#dynamic_security_group-source-destination-example)

#### DYNAMIC_INSTANCE (Source Destination) Arguments
* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` = `DYNAMIC_INSTANCE` - (Required) Type of the Address Object
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the `instance_id`
* `region` - (Required) The Region to restrict the scope of the `instance_id`
* `vpc_id` - (Required) The VPC/VNet ID to restrict the scope of the `instance_id`
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the `instance_id`
* `instance_id` - (Required) The Instance ID used to dynamically associate all IPs of the Instance
<br><br>For an example, see [DYNAMIC_INSTANCE (Source Destination) Example](#dynamic_instance-source-destination-example)

#### DYNAMIC_SUBNET (Source Destination) Arguments
* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` = `DYNAMIC_SUBNET` - (Required) Type of the Address Object
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the `subnet_id`
* `region` - (Required) The Region to restrict the scope of the `subnet_id`
* `vpc_id` - (Required) The VPC ID to restrict the scope of the `subnet_id`
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the `subnet_id`
* `subnet_id` - (Required) The Subnet ID used to dynamically associate the CIDR of the Subnet
<br><br>For an example, see [DYNAMIC_SUBNET (Source Destination) Example](#dynamic_subnet-source-destination-example)

#### DYNAMIC_USER_DEFINED_TAG (Source Destination) Arguments
* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` = `DYNAMIC_USER_DEFINED_TAG` - (Required) Type of the Address Object
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the `tag_list`
* `region` - (Optional) The Region to restrict the scope of the `tag_list`
* `vpc_id` - (Optional) The VPC ID to restrict the scope of the `tag_list`
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the `tag_list`
* `tag_list` - (Required) The set of one or more blocks where each block represents a Tag key-value pair and Resource type. The set of blocks is operated on by an AND operator. `tag_list` structure [defined below](#tag-list). The set of `tag_list` arguments are used to dynamically associate the IPs or CIDRs of Instances, Subnets and VPCs/Vnets that match the set of Tag key-value pairs.
<br><br>For an example, see [DYNAMIC_USER_DEFINED_TAG (Source Destination) Example](#dynamic_user_defined_tag-source-destination-example)

#### DYNAMIC_SERVICE_ENDPOINTS (Source Destination) Arguments
* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` = `DYNAMIC_SERVICE_ENDPOINTS` - (Required) Type of the Address Object
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the `service_endpoint_name`
* `region` - (Required) The Region to restrict the scope of the `service_endpoint_name`
* `service_endpoint_name` - (Required) The Service Endpoint used to dynamically associate all CIDRs of the Service Endpoint
<br><br>For an example, see [DYNAMIC_SERVICE_ENDPOINTS (Source Destination) Example](#dynamic_service_endpoints-source-destination-example)

#### GEO_IP (Source Destination) Arguments
* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` = `GEO_IP` - (Required) Type of the Address Object
* `value` - (Required) A list of Geo IPs defined by their Country name.  A full list of Country names can be obtained from the [GeoNames Countries](https://www.geonames.org/countries/) site.
* `match_xff` - (Optional) `true` or `false`.  Defines whether the IP information available in the X-Forwarded-For HTTP request header should be evaluated. When not specified, the default value is `false`. (Even though this argument is optional, it is recommended to specify a value explicitly, as the default value may change in the future).
<br><br>For an example, see [GEO_IP (Source Destination) Example](#geo_ip-source-destination-example)

#### GROUP (Source Destination) Arguments
* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` = `GROUP` - (Required) Type of the Address Object
* `address_group_ids` - (Required) A list of `address_id` to be grouped together
<br><br>For an example, see [GROUP (Source Destination) Example](#group-source-destination-example)

#### DYNAMIC_ASG (Source Destination) Arguments
* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` = `DYNAMIC_ASG` - (Required) Type of the Address Object(Azure Only)
* `csp_account_name` - (Required) Name of the Azure CSP account onboarded into Valtix
* `region` - (Required) ID of Azure Region where application security group is located 
* `resource_group` - (Required) Name of Azure Resource Group where application security group is located
* `application_security_group_id` - (Required) ID of Azure Application Security Group (ASG), for example : /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Network/applicationSecurityGroups/<APPLICATION_SECURITY_GROUP>
<br><br>For an example, see [DYNAMIC_ASG (Source Destination) Example](#dynamic_asg-source-destination-example)

### Tag List
A `tag_list` block representing a Tag key-value pair requires the following arguments:
* `tag_key` - (Required) The Tag Key specified in a Tag key-value pair
* `tag_value` - (Required) The Tag Value specified in a Tag key-value pair
* `resource_type`- (Optional) The Resource type to restrict the scope of the Tag Key-Value pair. Applicable values: `RESOURCE_INSTANCE`, `RESOURCE_VPC`, `RESOURCE_SUBNET`, `RESOURCE_LOAD_BALANCER`, `RESOURCE_SECURITY_GROUP`, `RESOURCE_ASG`. If not supplied, the default value is `RESOURCE_INSTANCE`. 

## Attribute Reference
* `id` - ID of the Address Object resource that can be referenced in other resources (e.g., *valtix_service_object*, `address_group_ids`)
* `address_id` - (Deprecated) Same as the `id` attribute

## Import
[*Public Preview*] Address Object resources can be imported using the resource `id`:

```hcl
$ terraform import valtix_address_object.ip_cidr_ag 10
```