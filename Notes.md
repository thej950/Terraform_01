# Terraform Introduction
1. terraform is IAAS | it will automates infrastrucher
2. Define infrastrucher state 
3. Ansible, puppet and chef are automates mostly OS related tasks.
	- Define machines states 
4. Terraform Automate infrastrucher itself 
	- Like AWS, GCP, Azure, digital ocean etc..
5. Terraform works with automation softwares like ansible after infra is setup and ready 
6. No pragramming, Its own syntax similar to JSON. 

```bash
$ terraform validate # command will validate terraform code is perfect or not if any syntax error it will show 

$ terraform init #this commands will initialise work loaction of terrafom 

$ terraform plan  #this commads will plan to execute in terraform file it will tell how many resources are going to reflect actully 

$ terraform apply #command this will perform actual work on the cloud provider like aws or azure 

$ terraform fmt #command will format the our code in a proper way it makes our terraform code more readable  


$ terraform destroy #will delete current activity on aws cloude environment
 
```
- terraform.tfstate this will maintaine the ec2 instance complete information like current state of ec2 instance in JSON format ( like terraform maintaine state in this file ) 

- terraform.tfstate.backup file containe backup of terraform.tfstate file information also 

```tf	
provider "aws" {
    access_key = "" # here need to specify accesskey
    secret_key = "" # here need to specify secretkey
    region     = "ap-south-1"
}
```

# Access AWS cloud from local system 
- create a User with adminstratorAccess download Accesskey and seretAcessKey To conect aws cloud 
- Download awscli tool in windows 
    Refer Link: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
- Now perform aws configure command To access aws cloud 
```bash    
$ aws configure 
AccessKey: # give accesskey
secretAccessKey: # give secretkey
region: ap-southeast-1
text: text
```
- Now we able to Connected to aws cloud 



# Terraform aws credentials
- there are multiple ways to cofigure accesskey and secretkey most popular ways are three ways 
    
a. Hard coded AWS credentials 
b. Shared credentials file
c. Export AWS credentials enviroments variables 


## Hard coded AWS credentials
- This is not recomended in production environment 
- uses of Hard coded aws credentials in main terraform file
```t
provider "aws" {
    accesskey = "" # here need to specify accesskey
    secretkey = "" # here need to specify secretkey
    region	  = "us-east-1"
}
```

## Shared credentials file
- this is a way to store aws credentials in a secret file and pass into main terrafile 
- way to pass Shared aws credentials inside main terraform file 

```t
provider "aws" {
    region	= "us-east-1"
    shared_credentials_file	= "C:\Users\DELL\.aws\credentials"
}
```

- this is when we excute aws configure automatically inside credentials file will be created in that perticular location (C:\Users\DELL\.aws\credentials)
- other wise create a file with below syntax
    

```bash
$ vim credentials

[default]
aws_access_key_id = AKIARVZBY3GJSE3OYOOB
aws_secret_access_key = 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
```
## Export AWS credentials enviroments variables 
- This is a way to export accesskey and secretkey into variables and pass that variables inside main terraform file 
- To move accesskey and secretkey into system  
```bash
$ export AWS_ACCESS_KEY_ID="AKIARVZBY3GJSE3OYOOB"

$ export AWS_SECRET_ACCESS_KEY="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"
```

- To usage of those inside main terraform file 
```t   
provider "aws" {
    accesskey = "us-east-1"
}
```
- after excute terraform init if it is excuting successfully then those access key and secret access key will be exported correctly other wise not 

# Terraform Datasources
- Terraform Datasource it is way of fetch and use existing information or resources from the infrastrucher that we are actually managing 
- it will allow to query the external systems (like cloud providers API databases etc... ad use the obtained data wothin our terraform configuration file )
- we can fetch the data on the providers and use inside terraform file and fetch the data from resource block and providers 
Basic Syntax of Datasource

```t
data "aws_ami" "latest_amazon_linux" {
    most_recent = true
    owners      = ["amazon"]

    filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
    }
}

resource "aws_instance" "example" {
    ami           = data.aws_ami.latest_amazon_linux.id
    instance_type = "t2.micro"

    # other instance configurations...
}
```
- From above syntax data block search for amazon linux machine which is latest amazon linux machine
- From above resource block we are using that latest amazon linux machine inside resource block to launch and instance 

- The aws_ami data source queries AWS to find the latest Amazon Linux AMI.
- The resulting AMI information is stored in the data.aws_ami.latest_amazon_linux variable.
- The aws_instance resource then uses the AMI ID obtained from the data source to launch an EC2 instance.  

# Terraform output values
- using terraform output values we able to print that information on terminal in a flexible way 

```t
data "aws_instance" "myawsinstance" {
filter {
name = "tag:Name"
values = ["Terraform EC2"]
}

depends_on = [
"aws_instance.ec2_example"
]
} 

output "fetched_info_from_aws" {
value = data.aws_instance.myawsinstance
}
```
- From above basic terraform code in the data source block defining instance to pick and hold on perticular data.aws_instance.myinstance 
- Excute and print those instance information at the time of output block ( suppose like if we reqired public_ip or any image information ) 

- For to print image public ip 
```t
output "fetched_info_from_aws" {
    value = data.aws_instance.myawsinstance.public_ip
}
```
- from above file it will print public ip of that perticular myawsinstance 


> Note: Both data sources and output values contribute to the flexibility and reusability of Terraform configurations. Data sources enable you to dynamically fetch information during the planning and provisioning phases, while output values allow you to expose relevant information for further consumption or integration with other systems.

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

# install terraform inside windows through CLI 
```powershell
$ choco install terraform
$ choco install awscli # this is for aws commands to work 
```

# install terraform inside windows 
Refer Link: https://developer.hashicorp.com/terraform/install
- setup environment path in windows to work terraform commands 
    
# Terraform Locals

- In programming world we normally have concept of Locals similarly to that in Terraform also we have cocept of Terraform Locals 
- terraform locals are only accessible within that function or witin this scope only of terraform file 

1. value assignments: terraform locals do not chage their value once it is assigned we have to re-assign a new value 
2. power of Expression: Apart from static value assignment terraform uses power of expression like instead of using same words so many times include that word in locals pass that resources same like local variables 

To pass Locals In main.tf file Syntax:
```t
## Example of local with static value 

locals {
    my_local = "value"
}
```

- We can pass Two types of Locals in Main.tf file 
1. static value 
2. Dynamic value 

Dynamic value Syntax:
```t
## Example of local with dynamic value 

locals {
    my_local = "${var.my_variable_value}"
}
```
```t
# Examples of using Dyamic value 
    locals {
    my_local = "${var.ec2_instance_name}"
}
```
```t
# Supose To pass locals in main.tf file 
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_vpc" staging-vpc" {
    
    cidr_block	= "10.5.0.0/16"
    
    tags	= {
        Name = "staging-vpc-tag"
        }
    }

resource "aws_subnet" "staging-subnet" {
    vpc_id = aws_vpc.staging-vpc.id
    cidr_block = "10.5.0.0/16"
    
    tags = {
        Name = "staging-subnet-tag"
    }
}


resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  "t2.micro"
    subnet_id	 = aws_subnet.staging-subnet.id
    
    tags = {
        Name = "staging - Terraform EC2" 
    }
}
```
- From above main.tf file containe staging word multiple times for that we can convert that word into local variable 
- below is the file 
```t
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

locals {
    staging_env = "staging" 
    }

resource "aws_vpc" "staging-vpc" {
    
    cidr_block	= "10.5.0.0/16"
    
    tags	= {
        Name = "${local.staging_env}-vpc-tag"
        }
    }

resource "aws_subnet" "staging-subnet" {
    vpc_id = aws_vpc.staging-vpc.id
    cidr_block = "10.5.0.0/16"
    
    tags = {
        Name = "${local.staging_env}-subnet-tag"
    }
}


resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  "t2.micro"
    subnet_id	 = aws_subnet.staging-subnet.id
    
    tags = {
        Name = "${local.staging_env} - Terraform EC2" 
    }
}
```

- Now both files will work same first file not using local varibale in second file used locals 

Reference From J-hooq website: https://jhooq.com/how-to-use-terraform-locals/

# Terraform Loops
- understanding terraform for each and for loop 
- In terraform working with large number code use in terraform for each and for loop for simplify the tasks 

## Loops with Count:
- Iterate List using count

# To create multiple users using list 
```t
variable "user_names" {
    description = "IAM usernames"
    type        = list(string)
    default     = ["user1", "user2", "user3"]
}
```
- list it is type of variable it containe fields starting from 0,1,2 ( 0 is user1 and 1 is a user2 and 2 is user3 ) from above list type in varibale 
- from the above information we are creating 3 IAM users 
```t
resource "aws_iam_user" "example" {
    count = length(var.user_names)
    name  = var.user_names[count.index]
}
```
```t
# Below is the complete terraform file to create 3 iam users 
provider "aws" {
    region     = "eu-central-1"
    access_key = "XXXXXXXXXXXXXXXX"
    secret_key = "XXXXXXXXXXXXXXXX"
}
resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  "t2.micro"
    count = 1

    tags = {
            Name = "Terraform EC2"
    }

}

resource "aws_iam_user" "example" {
    count = length(var.user_names)
    name  = var.user_names[count.index]
}

variable "user_names" {
    description = "IAM usernames"
    type        = list(string)
    default     = ["user1", "user2", "user3"]
}
```
- from above file create ec2 instance and 3 iam users 

```t
# using set 
#Step 1: Create a set
variable "my_set" {
    type    = set(string)
    default = ["value1", "value2", "value3"]
}

#Step 2: Convert set to list
locals {
    my_list = tolist(var.my_set)
}

#Step 3: Use count to iterate
resource "my_resource" "example" {
    count = length(local.my_list)

    name = local.my_list[count.index]
    # Additional resource configuration...
}
```
> Reference websites: Jhooq -> https://jhooq.com/terraform-for-and-for-each-loop/

# Terraform Modules
- terraform modules are used to segregate task in a seperate foleders and used them accordingly whenever required with one main terraform file 
- modules it is concept in terraform to organise of terraforms file with multiple cocepts in a single main terraform file 
    
# Terraform output values:
- this terraform output values will print desired information what we want to print on terminal like instance public ip after creation and many more etc...
- Terraform output values can help you to print the attributes reference(arn, instance_state, outpost_arn, public_ip, public_dns etc) on your console.
```t
# Syntax of Terraform output values: 
    
    output "random_output" {
    value = "Hello this is output"
    }
```    
```t
# To see public ip if instance after launch using terraform 
provider "aws" {
    region     = "eu-central-1"
    access_key = <YOUR_ACCESS_KEY>
    secret_key = <YOUR_SECRET_KEY>
}

resource "aws_instance" "ec2_example" {
    
    ami           = "ami-0767046d1677be5a0"
    instance_type = "t2.micro"
    subnet_id = aws_subnet.staging-subnet.id
    
    tags = {
            Name = "test - Terraform EC2"
    }
}

output "my_console_output" {
    value = aws_instance.ec2_example.public_ip
} 
```
- from above file will be print o terminal public ip of machine after setup on aws 

**Break down of the above script**
- aws_instance - It is the keyword which you need write as is.
- ec2_example - The name which we have given at the time of creating aws_instance
- public_ip - You can use attributes reference page to get all the attribute which you want to print on console.
- The same approach can be followed to print - arn,instance_state,outpost_arn, password_data, primary_network_interface_id, private_dns, public_dns etc.

> Note: above main.tf file containe ouput values are included inside main.tf file that is ok nut in the organisatio we have create a output.tf file file name shoul be outpu.tf file 

**To avoid sensitive informaion**
```t
# syntax: 
output "my_console_output" {
    value = aws_instance.ec2_example.public_ip
    sensitive = true
} 
```

> Reference Websites: Jhooq: https://jhooq.com/how-to-use-terraform-output-values/ 

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

# Terraform state file 
- terraform state file containe current activity of main terraform configuration file 
- In terraform the state file is a crucial component that keeps track of the current state of infrastrucher 
- it is a JSON formatted file that containes information about the resources created by terraform their current configuration and other metadata
- the state file allow terraform to understand what resource it has provisioned and how they are configured, which is essential for operations like updates modifications and destruction.

**1. cotent of the state file**
- the state file containe detailed representation of the resources managed by terraform (includingtheir attributes, dependencies, and othr configuration details)
- it is also used to track the metadata such as provider information, version of terraform used, and variuos settings 

**2. Location of the state file**
- By default terraform create local terraform.tfstate file in the same directory or folder based on our terraform configurations files
- best practice to use terraform.tfstate file remotely like (amazon s3, and azure storage , hashicorp cosul) for collaborations of security and versio control of the file 

**3. Purpose of the state file**
- the terraform state file is critical to understand the existing infrastrucher and make decisions about how to modify it state file 
- it helps what what resources are already available ad what resources need to create and what need to detroy for our desired terraform configuration file 

**4. Cocurrency and Locking**
- terraform employes a Locking mechanism when working with state file to prevent concurrent operations from modifying the state file simenteneously. this will help avoid conflicts and data consistency 

**5. Remote state Backends**
- Remotes state file is recomended especially in team based environments 
- To provide centralized shared location is important this will help team collaborations and version control of state file

**6. Sensitive information**
- the state file containe sensitive information like Public ip and API keys and private ip 
- it is crucial to handle state file 
- maintaine secure storage options for state file 

**7. Managing the state**
- Terraform commands like terraform apply and terraform destroy and terraform import interact with state file is important To manage the state file properly 
- To avoid accidental deletion and modifications 

8. Take care of state file to maintaine and excute any commands properly To avoid accidental modifications and deleteion when perticularly using teams based environments 

> Note: saving state file Locally: whenever a single developer working with terraform workspace it is ok to store state file Locally 

**state file stores Remote:** whenever multiple team based evironments working with terraform workspace it is better save state file remotely like amazon s3 
```t
# To store state file Remotely in Amazon S3 

# Basic syntax to use in main terraformfile 

terraform {
    backend "s3" {
        bucket	= "thej-bucket"
        key		= "key/terraform.tfstate"
        region	= "us-east-1"
    }
}
```
- from above cotent that already existing bucket inside that bucket a key folder is created under that folder terraform.tfstate file is created or maintained remotely 

# Terraform userdata
- userdata concept in terraform it is helpful to setup terraform instance in the aws with customised applications installation in the system 
- To use user data block inside terraform code 
```t
# Basic syntax of user_data block 
resource "aws_instance" "myec2" {
    ami = "ami--0fc5d935ebf8bc3bc"
    instance_type = "t2.micro"

    # user_data attribute to create a text file inside EC2 instance
    user_data	= <<-EOF
                    #!/bin/bash
                    sudo apt update -y 
                    sudo apt install nginx -y
                    EOF
}
```

# Terraform Varaibles:
1. Terraform variables are used to store values and re-used them whenever they required for multiple times also  To pass into aws resource section To provision 

## Types of variables:
- there are two types of varibales 

1. simple value variable
2. collection variables 

- Simple value type variables are used to store only single value those are like ( string, number, bool ) 
- Collection type varibales are used to store multile values in a single variable ( List, map, set ) 
```t
# To define variables inside main.tf file 

variables "<our_variable_name>" {
    description	= "Instance type t2.micro"  ---------> Meaning Fule Description of the variable 
    type		= string  ---------------------------> here type of the variables Ex: string, number, bool, list, set, map; 
    default		= "t2.micro" ------------------------> variable default value 
```
```t
# To Pass string type variable inside main.tf file 
# string type variable syntax:
variable "instance_type" {
    description = "Instance type t2.micro"
    type        = string
    default     = "t2.micro"
} 
```		
```t
# Below is the sample terraform file without using variables
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {
    
    ami           = "ami-0767046d1677be5a0"
    instance_type = "t2.micro"
    
    tags = {
            Name = "Terraform EC2"
    }
}
```
-----------------------------------------------------------
from above For defining variable block you need
description : Small or short description about the purpose of the variable
type : What type of variable it is going to be ex - string, bool, number ...
default : What would be the default value of the variable
```t
# To pass variables inside main.tf file 
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  var.instance_type

    tags = {
            Name = "Terraform EC2"
    }
}

variable "instance_type" {
    description = "Instance type t2.micro"
    type        = string
    default     = "t2.micro"
}
```


# To pass Number Type variables 
1. Number type variable used for example like suppose we want create multiple instances 
```t
# sytax of Number type variable 
variable "instance_count" {
    description = "EC2 instance count"
    type        = number
    default     = 2
}  
```
```t
# To use number type variable inside main.tf file 
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  "t2.micro"
    count = var.instance_count

    tags = {
            Name = "Terraform EC2"
    }
}

variable "instance_count" {
    description = "EC2 instance count"
    type        = number
    default     = 2
} 
```
		
# Boolean type variable:
- this bool variable are used to setup true or false values inside our terraform file 
```t
# syntax of bool variable in tf file 
variable "enable_public_ip" {
    description = "Enable public IP address"
    type        = bool
    default     = true
}
```
```t
# Below is the using bool varaible inside main.tf file  
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}


resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  "t2.micro"
    count = 1
    associate_public_ip_address = var.enable_public_ip

    tags = {
            Name = "Terraform EC2"
    }

}

variable "enable_public_ip" {
    description = "Enable public IP address"
    type        = bool
    default     = true
}
```

# Terraform variables - list, set, map 
 - when it comes to collection input variables 
1. list
2. set
3. map

## 1.  List type varaibles 
- list variables containe multiple values in a single format of variable 
```t
# Syntax of List type variable 
variable "user_names" {
description = "IAM usernames"
type        = list(string)
default     = ["user1", "user2", "user3s"]
}
```
```t
# Below are used List type varibale in main.tf file 
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}
resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  "t2.micro"
    count = 1

    tags = {
            Name = "Terraform EC2"
    }

}

resource "aws_iam_user" "example" {
    count = length(var.user_names)
    name  = var.user_names[count.index]
}

variable "user_names" {
    description = "IAM usernames"
    type        = list(string)
    default     = ["user1", "user2", "user3"]
}
```
- from above main.tf file we are creating 3 IAM users 
```t
# To create a single user 
resource "aws_iam_user" "iam-users" {
    name = "raju"
}
```

## 2. 	Map variable type:
- terraform supports the map variable type where we can define the key-value pair 
- To use map varibales we can achive where we need to implement provosion in which environment suppose dev, test, prod environment like where we need to define project and environment, so we can use the map variable to achieve that 

```t
# Syntax of map varible to use in tf file 

variable "project_environment" {
    description = "project name and environment"
    type        = map(string)
    default     = {
    project     = "project-alpha",
    environment = "dev"
    }
}
```

```t
# To use map varibales in main.tf file 
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}
resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  "t2.micro"

    tags = var.project_environment

}


variable "project_environment" {
    description = "project name and environment"
    type        = map(string)
    default     = {
    project     = "project-alpha",
    environment = "dev"
    }
}
```		

# varibale.tf and terraform.tfvars files:
- In this variable.tf file is a specific file where we need to define our variables related to use in main.tf file example this file containe configuration which is required for main.tf file and also it containe optional default value for the variable 
- in the previouse time we pass variable in same main.tf file but it is not Recommended use this way to pass variables 

- Exmaple show case how variable.tf and are used in main.tf file 

**$ mkdir folder-1**
```t
# step1: create a $ vim foledr-1/main.tf file 

provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  var.instance_type

    tags = {
            Name = "Terraform EC2"
    }
}
```


```t
# step2: Now create  $ vim folder-1/variable.tf file 
variable "instance_type" {
    description = "Instance type t2.micro"
    type        = string
    default     = "t2.micro"
}
```
```bash
# step3: Now enter into folder-1 then perform init and plan and apply 
$ terraform init 
$ terraform plan
$ terraform apply 
```
> Note: From above Two files main.tf containe main configuration data which is need to spine up in aws and variable.tf containe variable content information which need to pass variable inside as var.variable_name

# terraform tfvars file usage 
- in this tfvars file containe actual value of variable.tf file 
- in this case main.tf file containe main configuration for to setup machine and variable.tf file containe variable information and tfvars containe actual information to setup 

**For Example to setup Ec2 instance** 

**step1: create a directory folder-2 and create main.tf and varibale.tf and tfvars files for store value of varibale** 

**$ mkdir folder-2**
**$ vim main.tf** 
```t
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  var.instance_type

    tags = {
            Name = "Terraform EC2"
    }
}
```
- create a varibale.tf file without any default values **$ vim variable.tf** 
```t
varibale "instance_type" {
}
```
		
**$ vim terraform.tfvars**
```t
instance_type="t2.micro"
```
```t
# Now perform init and plane and apply 
$ terraform init 
$ terraform plan 
$ terraform apply 
```
> Note: From above instance will be created with varibale and tfvars files  

# using Multiple terraform tfvars files  
- terraform tfvars files are used for to setup multiple environments like example using single - main.tf file we can setup multiple environments 
- like we can create  multiple .tfvars files based on our requirements 
- file name can be anything but extensions should be .tfvars 
- In this .tfvars file containe Actual value of variable.tf 
- Normally in previous time we created variable.tf file with default value but here we can avoid that default value inside variable.tf file we can include that in .tfvars file 

## For example To multiple tfvars files 

**step1: create a directory foledr-3 an create main.tf and variable.tf file and stage.tfvars for stagging environments and prod.tfvars for prod environment for provisioner machines**

**$ mkdir folder-3**

**$ vim main.tf**
```t
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  var.instance_type

    tags = {
            Name = "var.environment_name
    }
}
```
- From above main.tf file we pass two varibles one is instance_type and another one is tags name like environment_name 

**$ vim variable.tf** 
```t
variable "instance_type" {
}

variable "environment_name" {
}
```
- From above varibale.tf file containe two variable without default values 

- Below are we creating multiple tfvars file for different use cases
**$ vim stage.tfvars** 	
```t										
instance_type="t2.micro"

enviroment_name="stage"
```
- this staging file used to pass variables instance t2.micro and machine name as stage as a environment 

**$ vim prod.tfvars**
```t 
instance_type="t2.micro"

environment_name="prod" 
```
- this prod file used to pass variables instance t2.micro and mahine name as prod as a environment
			
# To supply different tfvars files in the same location 
```bash
$ terraform plan -var-file="stage.tfvars" # now only stage.tfvars files only reflected into main.tf file according to this machine name will come like stage as a name environment and machine will t2.micro 

$ terraform apply -vars-file="stage.tfvars"

$ terraform apply -var-file="prod.tfvars" # based on this only prod file will be refleted according to our use case 
```



# Terraform setting variable using command line var 
- setup machine witout using tfvars file and run by command line throgh only 

**ste1: create a main.tf file with varible and create a varibale.tf file without default value**

**$ mkdir folder-4**

**$ cd folder-4**
```t
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  var.instance_type

    tags = {
        Name = "Terraform EC2"
    }
}
```
- from above file we declare varibale 

**$ vim varibale.tf** 
```t    
variable "instance_type" {
}
```
- above file created for to pass instance but without using default value that we eed to achive using command line here 

- Now perform command with -var="instance_type=t2.micro" 
```bash
$ terraform apply -var="instance_type=t2.micro" # based on above file t2.micro machine will be created using main.tf file and variable.tf  file   
```

> Reference websites: J-hooq website: https://jhooq.com/terraform-variable-and-tfvars-file/
                                https://jhooq.com/terraform-input-variables/ 
        
# Terraform Workspace
- By help of this terraform workspace using single main.tf file used in differet workspace
- suppose i created dev-instances in developer workspace environmnet and later using that same main.tf file i switched to test workspace and using that main.tf file i created test-instances 
- using workspace concept we able to create multi environments using single main.tf file 
- terraform Workspace it is work like similar to git 
- like in git we are creating child branch and do work on child branch 
- similar like here also 

**To create Terraform workspace**
```bash
$ terraform workspace new dev # it will create new workspace and automatically moved to that workspace 
    
$ terraform workspace list # To check How many Workspace to list all workspaces 

	
$ terraform workspace show 	# To check active workspaces
		

$ terraform workspace select <workspace name> # To move One workspace to another workspace 
```

