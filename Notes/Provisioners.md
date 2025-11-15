# Terraform Provisioners: 
- there are different types of provisioners in terraform 
    1. file 
    2. loacl-exec 
    3. remote-exec
    
- there are also another provisioners which is vendor specifi provisioners 
    1. chef 
    2. puppet
    3. habitat
    4. salt-masterclass

## file provisioner:
-> file provisioner are used to copy local copy into remote machine 
```t
# syntax:
provisioner "file" {
    source		= "<local machine file path>"
    destination	= "<remote machine destination path>"
```
```t
# file provisioner usage in main.tf file 

provider "aws" {
    region     = "eu-central-1"
    access_key = "AKIATQ37NXBxxxxxxxxx"
    secret_key = "JzZKiCia2vjbq4zGGGewdbOhnacmxxxxxxxxxxxx"
    
}

resource "aws_instance" "ec2_example" {

    ami = "ami-0767046d1677be5a0"  
    instance_type = "t2.micro" 
    key_name= "aws_key"
    vpc_security_group_ids = [aws_security_group.main.id]

    provisioner "file" {
    source      = "/home/rahul/Jhooq/keys/aws/test-file.txt"
    destination = "/home/ubuntu/test-file.txt"
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
    egress = [
    {
        cidr_blocks      = [ "0.0.0.0/0", ]
        description      = ""
        from_port        = 0
        ipv6_cidr_blocks = []
        prefix_list_ids  = []
        protocol         = "-1"
        security_groups  = []
        self             = false
        to_port          = 0
    }
    ]
    ingress                = [
    {
        cidr_blocks      = [ "0.0.0.0/0", ]
        description      = ""
        from_port        = 22
        ipv6_cidr_blocks = []
        prefix_list_ids  = []
        protocol         = "tcp"
        security_groups  = []
        self             = false
        to_port          = 22
    }
    ]
}

resource "aws_key_pair" "deployer" {
    key_name   = "aws_key"
    public_key = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDbvRN/gvQBhFe+dE8p3Q865T/xTKgjqTjj56p1IIKbq8SDyOybE8ia0rMPcBLAKds+wjePIYpTtRxT9UsUbZJTgF+SGSG2dC6+ohCQpi6F3xM7ryL9fy3BNCT5aPrwbR862jcOIfv7R1xVfH8OS0WZa8DpVy5kTeutsuH5FMAmEgba4KhYLTzIdhM7UKJvNoUMRBaxAqIAThqH9Vt/iR1WpXgazoPw6dyPssa7ye6tUPRipmPTZukfpxcPlsqytXWlXm7R89xAY9OXkdPPVsrQA0XFQnY8aFb9XaZP8cm7EOVRdxMsA1DyWMVZOTjhBwCHfEIGoePAS3jFMqQjGWQd rahul@rahul-HP-ZBook-15-G2"
}
```

## Local exec provisioner:
- this provisioner will work on local machine where we excuting this terraform 
```t
# syntax:
provisioner "local-exec" {
    command = "touch hello-jhooq.txt"
}
```
```t
# usage local-exec in the main.tf file
provider "aws" {
    region     = "eu-central-1"
    access_key = "AKIATQ37NXBxxxxxxxxx"
    secret_key = "JzZKiCia2vjbq4zGGGewdbOhnacmxxxxxxxxxxxx"
    
}

resource "aws_instance" "ec2_example" {

    ami = "ami-0767046d1677be5a0"  
    instance_type = "t2.micro" 
    tags = {
        Name = "Terraform EC2"
    }

    provisioner "local-exec" {
    command = "touch hello-jhooq.txt"
    } 
} 
```

## Remote exec provisioner:
- this remote exec provisioner will be work on remote machines 
```t
# syntax of remote exec:
# To SSH into machine 

provisioner "remote-exec" {
    inline = [
        "touch hello.txt",
        "echo helloworld remote provisioner >> hello.txt",
    ]
}
```
```t
# usage remote exec in main.tf file 
provider "aws" {
    region     = "eu-central-1"
    access_key = "AKIATQ37NXBxxxxxxxxx"
    secret_key = "JzZKiCia2vjbq4zGGGewdbOhnacmxxxxxxxxxxxx"
    
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
    egress = [
    {
        cidr_blocks      = [ "0.0.0.0/0", ]
        description      = ""
        from_port        = 0
        ipv6_cidr_blocks = []
        prefix_list_ids  = []
        protocol         = "-1"
        security_groups  = []
        self             = false
        to_port          = 0
    }
    ]
    ingress                = [
    {
        cidr_blocks      = [ "0.0.0.0/0", ]
        description      = ""
        from_port        = 22
        ipv6_cidr_blocks = []
        prefix_list_ids  = []
        protocol         = "tcp"
        security_groups  = []
        self             = false
        to_port          = 22
    }
    ]
}


resource "aws_key_pair" "deployer" {
    key_name   = "aws_key"
    public_key = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDbvRN/gvQBhFe+dE8p3Q865T/xTKgjqTjj56p1IIKbq8SDyOybE8ia0rMPcBLAKds+wjePIYpTtRxT9UsUbZJTgF+SGSG2dC6+ohCQpi6F3xM7ryL9fy3BNCT5aPrwbR862jcOIfv7R1xVfH8OS0WZa8DpVy5kTeutsuH5FMAmEgba4KhYLTzIdhM7UKJvNoUMRBaxAqIAThqH9Vt/iR1WpXgazoPw6dyPssa7ye6tUPRipmPTZukfpxcPlsqytXWlXm7R89xAY9OXkdPPVsrQA0XFQnY8aFb9XaZP8cm7EOVRdxMsA1DyWMVZOTjhBwCHfEIGoePAS3jFMqQjGWQd rahul@rahul-HP-ZBook-15-G2"
}
```
> Reference website: https://jhooq.com/terraform-provisioner/ 
