# Resource: valtix_address_object
Manages address objects that can be used as source (for egress) or a target (backend) for ingress.

## Example Usage

### static address object
```hcl
resource "valtix_address_object" app1-ag {
  name            = "app1"
  description     = "Backend App"
  type            = "STATIC"
  value           = ["10.10.10.10"]
  backend_address = true
}
```

### dynamic address object (application) using a tag
```hcl
resource "valtix_address_object" app1-lb-tag {
  name            = "app1-lb-tag"
  description     = "application load balancer using a tag"
  type            = "DYNAMIC_APPLICATIONS"
  backend_address = true
  tag_list {
      tag_key = "tag-name1"
      tag_value = "tag-value1"
  }
}
```

### dynamic user defined tag object using one or more tags
```hcl
resource "valtix_address_object" udf-tag-ag {
  name            = "udf-tag-ag"
  description     = "ip addresses of instances using user defined tags"
  type            = "DYNAMIC_USER_DEFINED_TAG"
  tag_list {
      tag_key = "tag-name1"
      tag_value = "tag-value1"
  }
  tag_list {
     tag_key = "tag-name2"
     tag_value = "tag-value2"
  }
}
```

### dynamic vpc, use all the IP addresses with in the VPC (including public ips)
```hcl
resource "valtix_address_object" vpc1-ag {
  name             = "vpc1"
  description      = "Backend VPC"
  type             = "DYNAMIC_VPC"
  vpc_id           = data.aws_vpc.gw_vpc.id
  csp_account_name = valtix_cloud_account.aws1.name
  region           = var.aws_region
}
```


+### dynamic address object (group) using a collection of address objects
```hcl
resource "valtix_address_object" addr-group-ag {
  name            = "addr-group-ag"
  description     = "collection of address groups"
  type            = "GROUP"
  address_group_ids = [48, 52]
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
    * STORAGE_BUCKET
    * GROUP

## Additional arguments based on the type

## STATIC

* `value` - (Required) List of IP Address or FQDN. If the backend_address value is true, then this must be a list with one entry only.
* `backend_address` - (Optional) true/false. This must be set to true if this address object runs an application that's proxied/protected by the valtix_gw. The *value* attribute must be a single item list.

## DYNAMIC_APPLICATIONS

* `tag_key` - (Deprecated), name of the tag
* `tag_value` - (Deprecated), value of the tag
* `tag_list` - (Required), specify the tag_key and tag_value in the tag list
* `csp_account_name` - (Optional) Restrict the tag_key and tag_value for only the given csp_account_name (e.g "gcp1". This is the name of the account added via valtix_cloud_account)
* `vpc_id` - (OptionalRestrict the tag_key and tag_value for only the given vpc_id
* `region` - (Optional) Restrict the tag_key and tag_value for only the given region (e.g "us-east1")
* `resource_group` - (Azure only) Resource group name

## DYNAMIC_VPC

* `csp_account_name` - (Required) This is the name of the account added via valtix_cloud_account that selects the csp account to get the VPC
* `region` - (Required) Region where the VPC is defined
* `vpc_id` - (Required) VPC Id
* `resource_group` - (Azure only) Resource group name

## DYNAMIC_SUBNET

* `csp_account_name` - (Required) This is the name of the account added via valtix_cloud_account that selects the csp account to get the VPC
* `region` - (Required) Region where the VPC is defined
* `vpc_id` - (Required) VPC Id
* `subnet_id` - (Required) Subnet Id
* `resource_group` - (Azure only) Resource group name

## DYNAMIC_INSTANCE

* `csp_account_name` - (Required) This is the name of the account added via valtix_cloud_account that selects the csp account to get the VPC
* `region` - (Required) Region where the VPC is defined
* `vpc_id` - (Required) VPC Id
* `instance_id` - (Required) Instance Id
* `resource_group` - (Azure only) Resource group name

## DYNAMIC_SECURITY_GROUP

* `csp_account_name` - (Required) This is the name of the account added via valtix_cloud_account that selects the csp account to get the VPC
* `region` - (Required) Region where the VPC is defined
* `vpc_id` - (Required) VPC Id
* `security_group_id` - (Required) Security Group Id
* `resource_group` - (Azure only) Resource group name

## DYNAMIC_USER_DEFINED_TAG

* `csp_account_name` - (Optional) This is the name of the account added via valtix_cloud_account that selects the csp account to get the VPC
* `region` - (Optional) Region where the VPC is defined
* `vpc_id` - (Optional) VPC Id
* `tag_list` - (Required), list of tag key and tag value
* `resource_group` - (Azure only - optional) Resource group name

## DYNAMIC_SERVICE_ENDPOINTS

* `csp_account_name` - (Required) This is the name of the account added via valtix_cloud_account that selects the csp account to get the VPC
* `service_endpoint_name` - (Required) Service end point name

## GEO_IP

* `value` - (Required) Geo IP Value

## STORAGE_BUCKET

* `csp_account_name` - (Required) This is the name of the account added via valtix_cloud_account that selects the csp account to get the VPC
* `value` - (Required) Bucket Name


## GROUP

* `address_group_ids` - (Required) list of address ids that can be grouped together


## Attribute Reference

* `address_id` - Id of the profile that can be referenced in other resources (e.g. valtix_service_object)

