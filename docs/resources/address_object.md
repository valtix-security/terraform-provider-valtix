# Resource: valtix_address_object
Resource for creating and managing Address Objects that can be used as a Source and/or Destination in a Policy Ruleset Rule, or as a Target Backend Address in a Reverse Proxy Service Object.

## Example Usage

### Static Address Object using one IP Address used as a Reverse Proxy Target
```hcl
resource "valtix_address_object" "app1-ag" {
  name            = "app1"
  description     = "Backend App"
  type            = "STATIC"
  value           = ["10.10.10.10"]
  backend_address = true
}
```

### Dynamic User Defined Tag Address Object using tags used as a Reverse Proxy Target
```hcl
resource "valtix_address_object" "udf-tag-ag" {
  name        = "udf-tag-ag"
  description = "ip addresses of instances using user defined tags"
  type        = "DYNAMIC_USER_DEFINED_TAG"
  tag_list {
      tag_key   = "tag-name1"
      tag_value = "tag-value1"
  }
  tag_list {
     tag_key   = "tag-name2"
     tag_value = "tag-value2"
  }
}
```

### Static Address Object used as a Source/Destination in the Policy Rule
```hcl
resource "valtix_address_object" "app1-ag" {
  name        = "app1"
  description = "Static CIDRs / IP"
  type        = "STATIC"
  value       = ["10.0.0.0/16", "192.168.0.0/16", "172.16.1.15"]
}
```

### Dynamic VPC Address Object (all CIDRs associated with the VPC)
```hcl
resource "valtix_address_object" "vpc1-ag" {
  name             = "vpc1"
  description      = "Backend VPC"
  type             = "DYNAMIC_VPC"
  vpc_id           = data.aws_vpc.gw_vpc.id
  csp_account_name = valtix_cloud_account.aws1.name
  region           = var.aws_region
}
```

### Geo IP Address Object using three Geo IPs
```hcl
resource "valtix_address_object" "geo-ip_ag" {
  name        = "geo-ip-ag"
  description = "list of Geo IP country codes"
  type        = "GEO_IP"
  value       = ["Country1", "Country2", "Country3"]
}
```

### Group Address Object using other Address Objects
```hcl
resource "valtix_address_object" "addr-group-ag" {
  name              = "addr-group-ag"
  description       = "collection of address groups"
  type              = "GROUP"
  address_group_ids = [valtix_address_object.addr1.address_id, valtix_address_object.addr2.address_id]
}
```

## Argument Reference
* `name` - (Required) Name of the address object
* `description` - Description of the address object
* `type` - (Required)
    * [STATIC](#static-reverse-proxy-target)
    * [DYNAMIC_APPLICATIONS](#dynamic_application-reverse-proxy-target)
    * [DYNAMIC_VPC](#dynamic_vpc)
    * [DYNAMIC_SUBNET](#dynamic_subnet)
    * [DYNAMIC_INSTANCE](#dynamic_instance)
    * [DYNAMIC_SECURITY_GROUP](#dynamic_security_group)
    * [DYNAMIC_USER_DEFINED_TAG](#dynamic_user_defined_tag)
    * [DYNAMIC_SERVICE_ENDPOINTS](#dynamic_service_endpoints)
    * [GEO_IP](#geo_ip)
    * [GROUP](#group)

## Additional arguments based on the type

## STATIC (Reverse Proxy Target)
* `value` - (Required). The value must be provided as a list with a single IP Address or FQDN. e.g ["10.0.0.23"] or ["internal-lb-app1.app.com]
* `backend_address` - (Required) This argument must be set to *true*

## STATIC (Source/Destination in the Policy Rule)
* `value` - (Required) A list of IPs/CIDRs 

## DYNAMIC_APPLICATIONS (Reverse Proxy Target)
* `tag_list` - (Required) The set of one or more blocks where each block represents a *tag_key* and a *tag_value* argument. The set of blocks is operated on by an AND operator. Structure [defined below](#tag-list)
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the *tag_list*
* `vpc_id` - (Optional) The VPC ID to restrict the scope of the *tag_list*
* `region` - (Optional) The Region to restrict the scope of the *tag_list*
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the *tag_list*
* `backend_address` - (Required) This argument must be set to *true*

## DYNAMIC_VPC
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the VPCs/VNets
* `region` - (Required) The Region to restrict the scope of the VPCs/VNets
* `vpc_id` - (Required) The VPC/VNet ID used to dynamically associate all CIDRs for the VPC/VNet
* `resource_group` - (Azure only) The Resource Group to restrict the VNet

## DYNAMIC_SUBNET
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the Subnet
* `region` - (Required) The Region to restrict the scope of the Subnet
* `vpc_id` - (Required) The VPC ID to restrict the scope of the Subnet
* `subnet_id` - (Required) The Subnet ID used to dynamically associate the CIDR for the Subnet
* `resource_group` - (Azure only) The Resource Group to restrict the Subnet

## DYNAMIC_INSTANCE
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the Instance
* `region` - (Required) The Region to restrict the scope of the Instance
* `vpc_id` - (Required) The VPC ID to restrict the scope of the Instance
* `instance_id` - (Required) The Instance ID used to dynamically associate all IPs for the Instance
* `resource_group` - (Azure only) The Resource Group to restrict the Instance

## DYNAMIC_SECURITY_GROUP
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the Security Group
* `region` - (Required) The Region to restrict the scope of the Security Group
* `vpc_id` - (Required) The VPC ID to restrict the scope of the Security Group
* `security_group_id` - (Required) The Security Group ID used to dynamically associate all IPs for the Security Group
* `resource_group` - (Azure only) The Resource Group to restrict the Security Group

## DYNAMIC_USER_DEFINED_TAG
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the User Defined Tags
* `region` - (Optional) The Region to restrict the scope of the User Defined Tags
* `vpc_id` - (Optional) The VPC ID to restrict the scope of the User Defined Tags
* `tag_list` - (Required) The set of one or more blocks where each block represents a *tag_key* and a *tag_value* argument.  The set of blocks is operated on by an AND operator. Structure [defined below](#tag-list)
* `resource_group` - (Azure only) The Resource Group to restrict the User Defined Tags

## GEO_IP
* `value` - (Required) A list of Geo IPs defined by their Country name.  A full list of Country names can be obtained from the [GeoNames Countries](https://www.geonames.org/countries/) site.

## DYNAMIC_SERVICE_ENDPOINTS
* `csp_account_name` - (Required) The name of the CSP account onboarded into Valtix to restrict the scope of the Service Endpoint
* `service_endpoint_name` - (Required) The Service Endpoint used to dynamically associate all FQDNs for the Service Endpoint
* `region` - (Required) The Region to restrict the scope of the Service Endpoint

## GROUP
* `address_group_ids` - (Required) A list of Valtix Address Object IDs to be grouped together

## Tag List
* `tag_key` - (Required) The Tag Key used within a *tag_list* block
* `tag_value` - (Required) Tag Value used within a *tag_list* block

## Attribute Reference
* `address_id` - ID of the Address Object that can be referenced in other resources (e.g., *valtix_service_object*)