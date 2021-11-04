# Resource: valtix_address_object
Resource for creating and managing Address Objects that can be used as a Source and/or Destination in a Policy Ruleset Rule, or as a Target Backend Address in a Reverse Proxy Service Object.

## Example Usage

### Static Address Object using one IP Address
```hcl
resource "valtix_address_object" "app1-ag" {
  name            = "app1"
  description     = "Backend App"
  type            = "STATIC"
  value           = ["10.10.10.10"]
  backend_address = true
}
```

### Dynamic Application Tag Address Object using one Tag
```hcl
resource "valtix_address_object" "app1-lb-tag" {
  name            = "app1-lb-tag"
  description     = "application load balancer using a tag"
  type            = "DYNAMIC_APPLICATIONS"
  backend_address = true
  tag_list {
      tag_key   = "tag-name1"
      tag_value = "tag-value1"
  }
}
```

### Dynamic User Defined Tag Address Object using two Tags
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
  value       = ["China","North Korea","Russia"]
}
```

### Group Address Object using two Address Objects
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
    * STATIC
    * DYNAMIC_APPLICATIONS
    * DYNAMIC_VPC
    * DYNAMIC_SUBNET
    * DYNAMIC_INSTANCE
    * DYNAMIC_SECURITY_GROUP
    * DYNAMIC_USER_DEFINED_TAG
    * DYNAMIC_SERVICE_ENDPOINTS
    * GEO_IP
    * GROUP

## Additional arguments based on the type

## STATIC
* `value` - (Required) A list of IPs/CIDRs if used as a Src/Dest Address Object.  A single IP/FQDN if used as a Reverse Proxy Target Address Object.  Default is *false*.
* `backend_address` - (Optional) *true* / *false*. This argument must be set to *true* if used as a Reverse Proxy Target Address Object.  Default *false*.

## DYNAMIC_APPLICATIONS
* `tag_list` - (Required) The set of one or more blocks where each block represents a *tag_key* and a *tag_value* argument.  The set of blocks is operated on by an AND operator.
* `tag_key` - (Required) The Tag Key used within a *tag_list* block
* `tag_value` - (Required) Tag Value used within a *tag_list* block
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the *tag_list*
* `vpc_id` - (Optional) The VPC ID to restrict the scope of the *tag_list*
* `region` - (Optional) The Region to restrict the scope of the *tag_list*
* `resource_group` - (Azure only) The Resource Group to restrict the scope of the *tag_list*
* `backend_address` - (Required) *true*. This argument must be set to *true* if used as a Reverse Proxy Target Address Object.

## DYNAMIC_VPC
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the VPCs/VNets
* `region` - (Required) Region where the VPC is defined
* `region` - (Optional) The Region to restrict the scope of the VPCs/VNets
* `vpc_id` - (Required) The VPC/VNet ID used to dynamically associate all CIDRs for the VPC/VNet
* `resource_group` - (Azure only) The Resource Group to restrict the VNet

## DYNAMIC_SUBNET
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the Subnet
* `region` - (Optional) The Region to restrict the scope of the Subnet
* `vpc_id` - (Optional) The VPC ID to restrict the scope of the Subnet
* `subnet_id` - (Required) The Subnet ID used to dynamically associate the CIDR for the Subnet
* `resource_group` - (Azure only) The Resource Group to restrict the Subnet

## DYNAMIC_INSTANCE
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the Instance
* `region` - (Optional) The Region to restrict the scope of the Instance
* `vpc_id` - (Optional) The VPC ID to restrict the scope of the Instance
* `instance_id` - (Required) The Instance ID used to dynamically associate all IPs for the Instance
* `resource_group` - (Azure only) The Resource Group to restrict the Instance

## DYNAMIC_SECURITY_GROUP
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the Security Group
* `region` - (Optional) The Region to restrict the scope of the Security Group
* `vpc_id` - (Optional) The VPC ID to restrict the scope of the Security Group
* `security_group_id` - (Required) The Security Group ID used to dynamically associate all IPs for the Security Group
* `resource_group` - (Azure only) The Resource Group to restrict the Security Group

## DYNAMIC_USER_DEFINED_TAG
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the User Defined Tags
* `region` - (Optional) The Region to restrict the scope of the User Defined Tags
* `vpc_id` - (Optional) The VPC ID to restrict the scope of the User Defined Tags
* `tag_list` - (Required) The set of one or more blocks where each block represents a *tag_key* and a *tag_value* argument.  The set of blocks is operated on by an AND operator.
* `tag_key` - (Required) The Tag Key used within a *tag_list* block
* `tag_value` - (Required) Tag Value used within a *tag_list* block
* `resource_group` - (Azure only) The Resource Group to restrict the User Defined Tags

## GEO_IP
* `value` - (Required) A list of Geo IPs defined by their Country name.  A full list of Country names can be obtained from the [GeoNames Countries](https://www.geonames.org/countries/) site.

## DYNAMIC_SERVICE_ENDPOINTS
* `csp_account_name` - (Optional) The name of the CSP account onboarded into Valtix to restrict the scope of the Service Endpoint
* `service_endpoint_name` - (Required) The Service Endpoint used to dynamically associate all FQDNs for the Service Endpoint
* `region` - (Optional) The Region to restrict the scope of the Service Endpoint

## GROUP
* `address_group_ids` - (Required) A list of Address Object IDs to be grouped together

## Attribute Reference
* `address_id` - ID of the Address Object that can be referenced in other resources (e.g., *valtix_service_object*)