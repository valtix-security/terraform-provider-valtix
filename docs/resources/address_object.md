# Resource: valtix_address_object
Resource for creating and managing Address Objects that can be used as a Source or Destination (Src/Dest) in a Policy Ruleset Rule, or as a Target Backend Address (Reverse Proxy Target) in a Reverse Proxy Service Object.

## Example Usage

### STATIC (Source Destination) Example
```hcl
resource "valtix_address_object" "app1-ag" {
  name        = "app1"
  description = "Static IP/CIDR"
  type        = "STATIC"
  value       = ["10.0.0.0/16", "192.168.0.0/16", "172.16.1.15"]
}
```

### STATIC (Reverse Proxy Target) Example
```hcl
resource "valtix_address_object" "app1-ag" {
  name            = "app1"
  description     = "Static IP/FQDN (backend)"
  type            = "STATIC"
  value           = ["10.10.10.10"]
  backend_address = true
}
```

### DYNAMIC_APPLICATIONS (Reverse Proxy Target) Example
```hcl
resource "valtix_address_object" "apps1-ag" {
    name        = "apps1"
    description = "Dynamic Applications (backend)"
    type        = "DYNAMIC_APPLICATIONS"
    tag_list {
        tag_key   = "tag-key1"
        tag_value = "tag-value1"
    }
}
```

### DYNAMIC_VPC (Source Destination) Example
```hcl
resource "valtix_address_object" "vpc1-ag" {
    name             = "vpc1"
    description      = "Dynamic VPC ID"
    type             = "DYNAMIC_VPC"
    csp_account_name = valtix_cloud_account.aws1.name
    region           = var.aws_region
    vpc_id           = data.aws_vpc.vpc1.id
}
```

### DYNAMIC_USER_DEFINED_TAG (Source Destination) Example
```hcl
resource "valtix_address_object" "udt1-ag" {
    name        = "udt1"
    description = "Dynamic User Defined Tag"
    type        = "DYNAMIC_USER_DEFINED_TAG"
    tag_list {
        tag_key   = "tag-key1"
        tag_value = "tag-value1"
    }
    tag_list {
        tag_key   = "tag-key2"
        tag_value = "tag-value2"
    }
}
```

### GEO_IP (Source Destination) Example
```hcl
resource "valtix_address_object" "geoip1_ag" {
  name        = "geoip1"
  description = "GeoIP Country"
  type        = "GEO_IP"
  value       = ["Country1", "Country2", "Country3"]
}
```

### GROUP (Source Destination) Example
```hcl
resource "valtix_address_object" "group-ag" {
  name              = "group1"
  description       = "Address Object Group"
  type              = "GROUP"
  address_group_ids = [valtix_address_object.addr1.address_id, valtix_address_object.addr2.address_id]
}
```


## Argument Reference

### Common Arguments
* `name` - (Required) Name of the Address Object
* `description` - (Optional) Description of the Address Object
* `type` - (Required) Type of the Address Object
    * [STATIC](#static-arguments)
    * [DYNAMIC_APPLICATIONS](#dynamic_applications-reverse-proxy-target-arguments)
    * [DYNAMIC_VPC](#dynamic_vpc-source-destination-arguments)
    * [DYNAMIC_SECURITY_GROUP](#dynamic_security_group-source-destination-arguments)
    * [DYNAMIC_INSTANCE](#dynamic_instance-source-destination-arguments)
    * [DYNAMIC_SUBNET](#dynamic_subnet-source-destination-arguments)
    * [DYNAMIC_USER_DEFINED_TAG](#dynamic_user_defined_tag-source-destination-arguments)
    * [DYNAMIC_SERVICE_ENDPOINTS](#dynamic_service_endpoints-source-destination-arguments)
    * [GEO_IP](#geo_ip-source-destination-arguments)
    * [GROUP](#group-source-destination-arguments)

### STATIC Arguments

#### STATIC (Source Destination) Arguments
* `value` - (Required) A list of IPs/CIDRs 

#### STATIC (Reverse Proxy Target) Arguments
* `value` - (Required) A list containing a single IP/FQDN (e.g., `["10.0.0.23"]` or `["internal-lb-app1.app.com]`)
* `backend_address` - (Required) This argument must be set to `true`

### DYNAMIC Arguments

#### DYNAMIC_APPLICATIONS (Reverse Proxy Target) Arguments
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the `tag_list`
* `vpc_id` - (Optional) The VPC/VNet ID to restrict the scope of the `tag_list`
* `region` - (Optional) The Region to restrict the scope of the `tag_list`
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the `tag_list`
* `tag_list` - (Required) A single block that represents a Tag key-value pair associated with an Application Load Balancer. `tag_list` structure [defined below](#tag-list). The `tag_list` is used to dynamically associate the FQDN of the Application Load Balancer that matches the Tag key-value pair.  
* `backend_address` - (Required) This argument must be set to `true`

#### DYNAMIC_VPC (Source Destination) Arguments
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the `vpc_id`
* `region` - (Required) The Region to restrict the scope of the `vpc_id`
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the `vpc_id`
* `vpc_id` - (Required) The VPC/VNet ID is used to dynamically associate all CIDRs of the VPC/VNet

#### DYNAMIC_SECURITY_GROUP (Source Destination) Arguments
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the `security_group_id`
* `region` - (Required) The Region to restrict the scope of the `security_group_id`
* `vpc_id` - (Required) The VPC/VNet ID to restrict the scope of the `security_group_id`
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the `security_group_id`
* `security_group_id` - (Required) The Security Group ID used to dynamically associate all IPs of the Security Group

#### DYNAMIC_INSTANCE (Source Destination) Arguments
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the `instance_id`
* `region` - (Required) The Region to restrict the scope of the `instance_id`
* `vpc_id` - (Required) The VPC/VNet ID to restrict the scope of the `instance_id`
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the `instance_id`
* `instance_id` - (Required) The Instance ID used to dynamically associate all IPs of the Instance

#### DYNAMIC_SUBNET (Source Destination) Arguments
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the `subnet_id`
* `region` - (Required) The Region to restrict the scope of the `subnet_id`
* `vpc_id` - (Required) The VPC ID to restrict the scope of the `subnet_id`
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the `subnet_id`
* `subnet_id` - (Required) The Subnet ID used to dynamically associate the CIDR of the Subnet

#### DYNAMIC_USER_DEFINED_TAG (Source Destination) Arguments
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the `tag_list`
* `region` - (Optional) The Region to restrict the scope of the `tag_list`
* `vpc_id` - (Optional) The VPC ID to restrict the scope of the `tag_list`
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the `tag_list`
* `tag_list` - (Required) The set of one or more blocks where each block represents a Tag key-value pair associated with an EC2 Instance. The set of blocks is operated on by an AND operator. `tag_list` structure [defined below](#tag-list). The set of `tag_list` arguments are used to dynamically associate the IPs of the EC2 Instances that match the set of Tag key-value pairs.

#### DYNAMIC_SERVICE_ENDPOINTS (Source Destination) Arguments
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the `service_endpoint_name`
* `region` - (Required) The Region to restrict the scope of the `service_endpoint_name`
* `service_endpoint_name` - (Required) The Service Endpoint used to dynamically associate all CIDRs of the Service Endpoint


### Other Arguments

#### GEO_IP (Source Destination) Arguments
* `value` - (Required) A list of Geo IPs defined by their Country name.  A full list of Country names can be obtained from the [GeoNames Countries](https://www.geonames.org/countries/) site.

#### GROUP (Source Destination) Arguments
* `address_group_ids` - (Required) A list of `address_id` to be grouped together


### Tag List
A `tag_list` block representing a Tag key-value pair requires the following arguments:
* `tag_key` - (Required) The Tag Key specified in a Tag key-value pair
* `tag_value` - (Required) The Tag Value specified in a Tag key-value pair


## Attribute Reference
* `address_id` - The ID of the Address Object that can be referenced in other resources (e.g., *valtix_service_object*, `address_group_ids`)