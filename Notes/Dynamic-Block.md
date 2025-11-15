# Terraform Dynamic Block
- In terraform Dynamic blocks are used to reduced number lines in the terraform code 
- in main.tf file containe  multiple resources and same  lines multiple times for that using Dynamic bolcks we able to Reduce lines of code inside main.tf file 
- Dynamic block is very much similar to for loop 

```t
# syntax of dynamic block 
dynamic "my_dynamic_block" {
    for_each = list | map | set 
    iterator = iterator_name
    content {
        key = iterator_name.value
    }
}
```
- suppose there are launch instance for that instance we require to open ports ssh and http and https for this we have write multiple times ingress so here we able to avoid this using Dynamic Blocks 

example:
```t
resource "aws_security_group" "main" {
    name   = "resource_without_dynamic_block"
    vpc_id = data.aws_vpc.main.id

    ingress {
        description = "ingress_rule_1"
        from_port   = 443
        to_port     = 443
        protocol    = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
    }
    
    ingress {
        description = "ingress_rule_2"
        from_port   = 80
        to_port     = 80
        protocol    = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
    }

    tags = {
        Name = "AWS security group non-dynamic block"
    }
}
```

- observer below file 
```t
locals {
    ingress_rules = [{
        port        = 443
        description = "Ingress rules for port 443"
    },
    {
        port        = 80
        description = "Ingree rules for port 80"
    }]
}

resource "aws_security_group" "main" {
    name   = "resource_with_dynamic_block"
    vpc_id = data.aws_vpc.main.id

    dynamic "ingress" {
        for_each = local.ingress_rules

        content {
            description = ingress.value.description
            from_port   = ingress.value.port
            to_port     = ingress.value.port
            protocol    = "tcp"
            cidr_blocks = ["0.0.0.0/0"]
        }
    }

    tags = {
        Name = "AWS security group dynamic block"
    }
}
```

- From above observe both files 

- Below is is the actuall main.tf file with included used Dynamic blocks for security group and  launch instance 
```t
provider "aws" {
    region     = "eu-central-1"
    access_key = ""
    secret_key = ""
    
}

locals {
    ingress_rules = [{
        port        = 443
        description = "Ingress rules for port 443"
    },
    {
        port        = 80
        description = "Ingree rules for port 80"
    }]
}


resource "aws_instance" "ec2_example" {

    ami = "ami-0767046d1677be5a0"  
    instance_type = "t2.micro" 
    key_name= "aws_key"
    vpc_security_group_ids = [aws_security_group.main.id]

    provisioner "remote-exec" {
    inline = [
        "touch hello.txt",
        "echo helloworld remote provisioner >> hello.txt",
    ]
    }
    connection {
        type        = "ssh"
        host        = self.public_ip
        user        = "ubuntu"
        private_key = file("/home/rahul/Jhooq/keys/aws/aws_key")
        timeout     = "4m"
    }
}




resource "aws_security_group" "main" {
    name   = "resource_with_dynamic_block"
    vpc_id = data.aws_vpc.main.id

    dynamic "ingress" {
        for_each = local.ingress_rules

        content {
            description = ingress.value.description
            from_port   = ingress.value.port
            to_port     = ingress.value.port
            protocol    = "tcp"
            cidr_blocks = ["0.0.0.0/0"]
        }
    }

    tags = {
        Name = "AWS security group dynamic block"
    }
}




resource "aws_key_pair" "deployer" {
    key_name   = "aws_key"
    public_key = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDbvRN/gvQBhFe+dE8p3Q865T/xTKgjqTjj56p1IIKbq8SDyOybE8ia0rMPcBLAKds+wjePIYpTtRxT9UsUbZJTgF+SGSG2dC6+ohCQpi6F3xM7ryL9fy3BNCT5aPrwbR862jcOIfv7R1xVfH8OS0WZa8DpVy5kTeutsuH5suehdngba4KhYLTzIdhM7UKJvNoUMRBaxAqIAThqH9Vt/iR1WpXgazoPw6dyPssa7ye6tUPRipmPTZukfpxcPlsqytXWlXm7R89xAY9OXkdPPVsrQdkdfhnY8aFb9XaZP8cm7EOVRdxMsA1DyWMVZOTjhBwCHfEIGoePAS3jFMqQjGWQd rahul@rahul-HP-ZBook-15-G2"
}
```
Reference website: https://jhooq.com/terraform-dynamic-block/
