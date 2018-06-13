# aws_manage_subnets

## Description:

Manages AWS Virtual Private Clouds (VPCs) Subnets as follows:
- Running the [main.yml](./tasks/main.yml) play will create the subnets defined in `aws_subnets`.  
- Running the [get_subnet_facts.yml](./tasks/get_subnet_facts.yml) play will return information about the specified subnet.

## Behaviour:

**Feature:** Create AWS VPCs  
- **Given** valid AWS credentials
- **When** the script is executed
- **Then** the subnets are created if they do not exist

## Configuration:

Common variables used by this role:

| Variable | Description | Example |
|-----|-----|-----|
| **aws_region** | AWS region | See [AWS regions](http://docs.aws.amazon.com/general/latest/gr/rande.html#ec2_region) |
| **aws_access_key** | AWS access key | AKIAIOSFODNN7EXAMPLE |
| **aws_secret_key** | AWS secret key | wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY |
| **resource_tags** | List of project tags | see usage |

Variables specific to this role:

| Variable | Description | Example |
|-----|-----|-----|
| **aws_subnets** | List of Subnets to create. | see usage |
| **subnet_tag** | The `Name:` tag of subnet to get information about. | see usage |

## Usage:

How to invoke the role from a playbook:

```yaml
- name: Create AWS VPCs
  include_role:
    name: aws_manage_subnets
  vars:
    aws_access_key: AKIAIOSFODNN7EXAMPLE
    aws_secret_key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
    aws_region: eu-west-2  # London

    resource_tags:
      Name: "{{ resource_name }}"   # Set to subnet name
      Owner: "Acme Co."

    aws_subnets:
      - aws_vpc_subnet_name: "Test-Public-A"
        aws_vpc_subnet_cidr: "10.11.1.0/24"
        aws_vpc_tag: "Test A"
        aws_vpc_subnet_az: "{{ aws_region }}a"
    
      - aws_vpc_subnet_name: "Test-Public-B"
        aws_vpc_subnet_cidr:  "10.22.1.0/24"
        aws_vpc_tag: "Test A"
        aws_vpc_subnet_az: "{{ aws_region }}b"
```

How to get information about a VPC subnet:

```yaml
- name: Get VPC Subnet Information
  include_role:
    name: "aws_manage_subnets"
    tasks_from: "get_subnet_facts"
  vars:
    subnet_tag: "TEST-PUBLIC-A"
```

This will return facts in the variable `subnet_facts`.  The data returned is as follows:

```yaml
"subnet_facts": {
    "changed": false, 
    "failed": false, 
    "subnets": [
        {
            "availability_zone": "eu-west-2a", 
            "available_ip_address_count": 250, 
            "cidr_block": "10.12.1.0/24", 
            "default_for_az": "false", 
            "id": "subnet-123ab12a", 
            "map_public_ip_on_launch": "false", 
            "state": "available", 
            "tags": {
                "Name": "TEST-PUBLIC-A"
                "Owner": "Acme Co.", 
                "Project": "My Project"
            }, 
            "vpc_id": "vpc-7b51d285"
        }
    ]
}
```