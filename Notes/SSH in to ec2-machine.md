# Terraform SSH in to ec2-machine
**step1: generate ssh keys into local machine**
```bash
# ssh-keygen -t rsa -b 2048
---------------------------------
Generating public/private RSA key pair.
Enter file in which to save the key (/home/rahul/.ssh/id_rsa): /home/rahul/Jhooq/keys/aws/aws_key 
Enter passphrase (empty for no passphrase): 
Enter the same passphrase again: 
Your identification has been saved in /home/rahul/Jhooq/keys/aws/aws_key
Your public key has been saved in /home/rahul/Jhooq/keys/aws/aws_key.pub
The key fingerprint is:
SHA256:sAOjXyvJc2gnMrvxXA+qiaU9pUEvwl5ZG9Y2kZqRf5M rahul@rahul-HP-ZBook-15-G2
The key's randomart image is:
+---[RSA 2048]----+
|      . .        |
|     o o         |
|    o B . .      |
|  .. O B E       |
|....+ B S .      |
|..o=o= o         |
|..*=X *          |
| *oX O o         |
|o *++   .        |
+----[SHA256]-----+

```
- from above task a private key and public will be generated in to local machine with name aws_key and aws_key.pub 
- change permissiones to 400 to aws_key file 

**step2: generate pem file in aws cloud before running terraform with name aws_key.pem**
    
**step3: copy public key which is aws_key.pub paste in the main.tf file**
```t    
provider "aws" {
    region     = "eu-central-1"
    access_key = ""
    secret_key = ""
    
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
    public_key = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDbvRN/gvQBhFe+dE8p3Q865T/xTKgjqTjj56p1IIKbq8SDyOybE8ia0rMPcBLAKds+wjePIYpTtRxT9UsUbZJTgF+SGSG2dC6+ohCQpi6F3xM7ryL9fy3BNCT5aPrwbR862jcOIfv7R1xVfH8OS0WZa8DpVy5kTeutsuH5suehdngba4KhYLTzIdhM7UKJvNoUMRBaxAqIAThqH9Vt/iR1WpXgazoPw6dyPssa7ye6tUPRipmPTZukfpxcPlsqytXWlXm7R89xAY9OXkdPPVsrQdkdfhnY8aFb9XaZP8cm7EOVRdxMsA1DyWMVZOTjhBwCHfEIGoePAS3jFMqQjGWQd rahul@rahul-HP-ZBook-15-G2"
}
```

> Reference Website: https://jhooq.com/terraform-ssh-into-aws-ec2/ 

---



