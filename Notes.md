
# Terraform Introduction 

Terraform is an **IaC (Infrastructure as Code)** tool created by HashiCorp.
It helps you **automate, create, update, and destroy** infrastructure on cloud platforms like:

* AWS
* Azure
* GCP
* DigitalOcean
* VMware
* Kubernetes
  …and many more.

Terraform lets you **define your infrastructure in code**, so machines, networks, and services can be created **automatically**, instead of doing it manually from cloud consoles.

---

## ⭐ Key Concepts (Simple & Clear)

### **1. Terraform = Infrastructure Automation**

Tools like **Ansible, Puppet, Chef** mainly automate **OS-level tasks** like:

* Installing software
* Managing configurations
* Updating packages

But **Terraform automates the infrastructure itself**, such as:

* EC2 instances
* VPC
* Subnets
* IAM
* Load Balancers
* Databases
* Kubernetes clusters

---

### **2. Terraform Defines Infrastructure State**

Terraform doesn’t just create infra; it keeps track of what it built through **state files**.

---

### **3. No Programming Needed**

Terraform uses **HCL (HashiCorp Configuration Language)**
Looks similar to JSON but simpler and cleaner.

---

## 🧠 Terraform Workflow Summary

1. **Write** → You write `.tf` files
2. **Init** → Initialize project
3. **Plan** → See what Terraform will create/update/destroy
4. **Apply** → Terraform performs the changes
5. **Destroy** → Remove all resources when no longer needed

---

## 🛠️ Important Terraform Commands

### **1. Validate code**

```bash
terraform validate
```

Checks for syntax errors.

---

### **2. Initialize Terraform**

```bash
terraform init
```

Downloads required provider plugins and prepares working directory.

---

### **3. Format code**

```bash
terraform fmt
```

Fixes indentation & formatting to make code neat and readable.

---

### **4. See execution plan**

```bash
terraform plan
```

Shows what Terraform *will* create/modify/delete (but does not execute).

---

### **5. Apply the changes**

```bash
terraform apply
```

Creates actual infrastructure on cloud.

---

### **6. Destroy infrastructure**

```bash
terraform destroy
```

Deletes everything created by Terraform.

---

### **7. Show current state**

```bash
terraform show
```

Displays current infrastructure details from the tfstate file.

---

### **8. List resources in state**

```bash
terraform state list
```

---

### **9. View details of a resource**

```bash
terraform state show <resource_name>
```

---

### **10. Import existing resources**

```bash
terraform import <resource_type.name> <resource_id>
```

Useful when infrastructure was created manually.

---

### **11. Manual refresh**

```bash
terraform refresh
```

Refreshes state file with real cloud infrastructure (used for drift detection).

---

## 📁 Terraform State Files

### **terraform.tfstate**

Stores **current infrastructure details** in JSON format:

* EC2 IDs
* VPC IDs
* IP addresses
* Tags
* Dependencies

Terraform uses this to know what exists.

### **terraform.tfstate.backup**

Backup copy of the previous state file.

---

## 🔑 Sample Provider Block

```hcl
provider "aws" {
  region = "ap-south-1"
  access_key = "YOUR_KEY"
  secret_key = "YOUR_SECRET"
}
```



## **Key Features**
- **Define Infrastructure State**: Helps create, manage, and maintain resources in a consistent state.
- **Comparison to Ansible/Puppet/Chef**:
  - Ansible/Puppet/Chef are more focused on managing operating system-level configurations (e.g., software installation, configuration files).
  - Terraform focuses on managing infrastructure itself (e.g., VMs, networking, load balancers).
- **Automation**: Terraform integrates well with tools like Ansible for post-infrastructure setup tasks.
- **Declarative Syntax**: Uses its own HashiCorp Configuration Language (HCL), similar to JSON, for defining infrastructure.

---

## **State and Supporting Files**

#### **1. terraform.tfstate**
- Maintains the current state of the infrastructure as per the Terraform configuration.
- Tracks information about created resources (e.g., EC2 instance IDs, IPs, etc.).
- If a resource is destroyed, its details are automatically removed from this file.
- Stored in **JSON format**.

#### **2. terraform.tfstate.backup**
- Stores a backup of the previous state file (`terraform.tfstate`) before any changes are applied.
- Useful for recovering the state if the current `tfstate` file becomes corrupted.

#### **3. terraform.lock.hcl**
- Maintains the exact versions of providers and their dependencies used in the configuration.
- Ensures consistency across environments or team members by locking the provider versions.

#### **4. .terraform Directory**
- Created after running `terraform init`.
- Stores provider plugins and downloaded modules required for Terraform to operate.
- Keeps dependencies for the current project configuration.

---

## **Key Concepts**
- **Declarative**: Define "what" the infrastructure should look like; Terraform figures out "how" to achieve it.
- **Idempotent**: Running the same configuration multiple times results in the same infrastructure state.
- **State Management**: Tracks the resources in the `tfstate` file to know what changes to apply or remove.


> **NOTE:** Hardcoding keys is NOT recommended. Use environment variables or shared credentials.

---

## ⭐ Summary (Easy to Remember)

* Terraform = Infrastructure Automation Tool
* Works with AWS, Azure, GCP, etc.
* Uses HCL (simple syntax like JSON)
* Manages infrastructure lifecycle
* Uses state file to track resources
* Fully automates creation & destruction of infra

---




# Access AWS Cloud from Your Local System

To manage AWS services from your laptop/desktop, you need to configure **AWS CLI** with your AWS credentials.

---

## ✅ Step 1: Create an IAM User in AWS

* Go to **IAM → Users → Create User**
* Assign policy: **AdministratorAccess** (only for learning; not recommended for production)
* Download the **Access Key** and **Secret Access Key**

These keys allow your local system to authenticate with AWS.

---

## ✅ Step 2: Install AWS CLI on Windows

Download the AWS CLI installer from the official page:

🔗 [https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

Install it like a normal software package.

---

## ✅ Step 3: Configure AWS Credentials

Open **Command Prompt / PowerShell** and run:

```bash
aws configure
```

You will be prompted to enter:

```
AWS Access Key ID:        # enter access key
AWS Secret Access Key:    # enter secret key
Default region name:      ap-southeast-1
Default output format:    text
```

---

## ✅ Step 4: Verify the Connection

Run a simple command:

```bash
aws s3 ls
```

If it lists S3 buckets (or shows empty result), your local system is successfully connected to AWS.

---

## ⭐ Summary

* Create IAM User → get AccessKey + SecretKey
* Install AWS CLI
* Run `aws configure`
* AWS CLI is now connected to your AWS Account

---

If you want, I can also explain:
✔ how to access AWS from Linux/Mac
✔ how to switch multiple AWS profiles
✔ how to avoid exposing access keys (best practices)

#  Terraform AWS Credentials

To allow Terraform to communicate with AWS, we must provide **AWS Access Key** and **Secret Key**.
There are **three popular ways** to configure credentials:

---

## ✅ 1. Hard-coded AWS Credentials (NOT Recommended)

You directly write credentials inside the Terraform file.

❌ Not secure
❌ Never use in production
❌ Exposes secrets in Git

### Example

```hcl
provider "aws" {
  region     = "us-east-1"
  access_key = "YOUR_ACCESS_KEY"
  secret_key = "YOUR_SECRET_KEY"
}
```

Use only for testing or learning—not for real projects.

---

## ✅ 2. Shared Credentials File (Recommended)

AWS stores your credentials in a file after running:

```bash
aws configure
```

This creates:

```
C:\Users\DELL\.aws\credentials   (Windows)
~/.aws/credentials               (Linux/Mac)
```

### Example Credentials File

```
[default]
aws_access_key_id     = AKIA*************
aws_secret_access_key = sXgKUS***********
```

### Use it in Terraform

Terraform reads the credentials automatically OR you can specify the path:

```hcl
provider "aws" {
  region                  = "us-east-1"
  shared_credentials_files = ["C:/Users/DELL/.aws/credentials"]
  profile                 = "default"
}
```

✔ Secure
✔ Most commonly used
✔ Works with multiple profiles

---

## ✅ 3. Environment Variables (Best for CI/CD)

You export AWS keys into environment variables at OS level.

### Set environment variables (Linux/Mac)

```bash
export AWS_ACCESS_KEY_ID="AKIA************"
export AWS_SECRET_ACCESS_KEY="sXgKUS************"
```

### Set in Windows (PowerShell)

```powershell
setx AWS_ACCESS_KEY_ID "AKIA*****"
setx AWS_SECRET_ACCESS_KEY "sXgKUS*****"
```

### Terraform Usage

You **do not** need to write keys in Terraform.

Just write:

```hcl
provider "aws" {
  region = "us-east-1"
}
```

Terraform automatically picks credentials from the environment.

✔ Very secure
✔ Best for Jenkins / GitHub Actions / DevOps pipelines
✔ Avoids hard coding

---

## ⭐ Interview Summary (Short Answer)

**Q: How do you configure AWS credentials in Terraform?**

**A:**
“There are three popular ways:

1. Hard-coded credentials (not recommended).
2. Shared credentials file created by `aws configure`.
3. Environment variables (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`)—best for CI/CD.
   In real-time we generally use shared credentials or environment variables.”

---


# Terraform Data Source (Simple Explanation)

**Terraform Data Sources** are used to **fetch and use existing information** from your infrastructure or external systems.
They allow Terraform to **query cloud provider APIs, databases, or existing resources**, and use that data inside your Terraform configuration.

With data sources, you can fetch:

* Existing AMIs
* VPC IDs
* Subnet IDs
* Security Groups
* IAM roles
* Any resource already present in AWS

This avoids hardcoding values and makes the Terraform code dynamic.

---

## ✅ Basic Syntax of a Data Source

```hcl
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
}
```

---

## 🔍 Explanation (Simple & Clear)

* The **data block** queries AWS to find the **latest Amazon Linux 2 AMI**.
* Terraform stores the result in:
  `data.aws_ami.latest_amazon_linux`
* In the **aws_instance** resource, we use:
  `ami = data.aws_ami.latest_amazon_linux.id`
  This launches an EC2 instance using the **latest Amazon Linux AMI** automatically.

---

## ⭐ Why Data Sources Are Useful?

* No need to manually search AMI IDs
* Avoid hard-coding values
* Always fetch **latest** or **existing** resource values
* Makes code reusable and dynamic

---
## QA for datasource
**1. What is a Data Source in Terraform?**

A data source is used to **fetch existing information** from cloud providers or external systems so Terraform can use that data inside configurations. It is **read-only**.

---

**2. Why do we use Data Sources?**

To avoid hardcoding values like AMI IDs, VPC IDs, Subnet IDs, and to always fetch the **latest or existing** resource values dynamically.

---

**3. Difference between Resource and Data Block?**

* **Resource** → creates/updates infrastructure
* **Data** → reads existing infrastructure (no changes)

---

**4. Real-time Example of a Data Source?**

```hcl
data "aws_ami" "latest" {
  most_recent = true
  owners = ["amazon"]
}
```

This fetches the **latest Amazon Linux AMI**.

---

**5. How do you use a Data Source value inside a resource?**

```
ami = data.aws_ami.latest.id
```

---


#  Terraform Output Values (Simple Explanation)

Terraform **output values** allow you to **print important information** on the terminal after running `terraform apply`.
This helps you easily see details like:

* Public IP
* AMI ID
* VPC ID
* Instance ID

Outputs are useful when other tools or modules need this information.

---

## ✅ Example: Using Data Source + Output Value

### **1. Fetch existing EC2 instance**

```hcl
data "aws_instance" "myawsinstance" {
  filter {
    name   = "tag:Name"
    values = ["Terraform EC2"]
  }

  depends_on = [
    "aws_instance.ec2_example"
  ]
}
```

Here, the data source searches AWS for an instance with the tag **Name = Terraform EC2** and stores the details in:

```
data.aws_instance.myawsinstance
```

---

## ✅ Print the full instance information

```hcl
output "fetched_info_from_aws" {
  value = data.aws_instance.myawsinstance
}
```

This prints all details of the instance on the terminal.

---

## ✅ Print only the Public IP

```hcl
output "fetched_info_from_aws" {
  value = data.aws_instance.myawsinstance.public_ip
}
```

This prints only the **public IP** of that instance.

---

## ⭐ Note

* **Data sources** fetch existing data dynamically.
* **Output values** display important information after provisioning.
  Together they make Terraform more flexible and reusable.

---

## 🔥 Top 2 Interview Q&A

### **1. What are Output Values in Terraform?**

Output values allow Terraform to **display useful information** (like IPs, IDs, ARNs) after running `terraform apply`.
They help in sharing data with users, scripts, and other modules.

---

### **2. How do Data Sources and Output Values work together?**

Data sources **fetch existing cloud information**, and output values **print that information**.
Example:
Data source gets an EC2 instance → Output prints its public IP.

---

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
    
Here is a **clean, simple-English, interview-ready version** of your Terraform **Locals** explanation.
I used your content and improved formatting + added clarity where needed.

---

# Terraform Locals 

In programming, a **local variable** is used only inside a function or block.
Terraform also has the same concept called **locals**.

**Terraform locals** allow you to store values that you want to reuse multiple times in your Terraform file.
They help reduce repetition and make your code cleaner and more readable.

---

## ✅ Why Do We Use Terraform Locals?

### 1️⃣ **Avoid repetition**

If the same word or value appears many times (like `"staging"` or `"t2.micro"`), you can save it in `locals` and use it everywhere.

### 2️⃣ **Easy to update**

If you want to change a value, change it only in the locals block instead of updating many places.

### 3️⃣ **Static or Dynamic values**

Locals support:

* **Static values** → fixed strings
* **Dynamic values** → use expressions or combine variables

### 4️⃣ **Locals cannot change at runtime**

Once assigned, the value remains fixed unless you update the code.

---

## ✅ Basic Syntax of Terraform Locals

### **Static value**

```hcl
locals {
  my_local = "value"
}
```

### **Dynamic value (using variables)**

```hcl
locals {
  my_local = var.ec2_instance_name
}
```

---

## 📝 Before using locals (repeating “staging” multiple times)

```hcl
resource "aws_vpc" "staging-vpc" {
  cidr_block = "10.5.0.0/16"

  tags = {
    Name = "staging-vpc-tag"
  }
}
```

This becomes hard to maintain when repeated many times.

---

## 📝 After using locals (clean and reusable)

```hcl
locals {
  staging_env = "staging"
}

resource "aws_vpc" "staging-vpc" {
  cidr_block = "10.5.0.0/16"

  tags = {
    Name = "${local.staging_env}-vpc-tag"
  }
}
```

Now if you change `staging` → `production`, you change it only once in locals.

---

## 🎯 Full Example With Locals

```hcl
provider "aws" {
  region     = "eu-central-1"
  access_key = "<INSERT_YOUR_ACCESS_KEY>"
  secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

locals {
  staging_env = "staging"
}

resource "aws_vpc" "staging-vpc" {

  cidr_block = "10.5.0.0/16"

  tags = {
    Name = "${local.staging_env}-vpc-tag"
  }
}

resource "aws_subnet" "staging-subnet" {
  vpc_id     = aws_vpc.staging-vpc.id
  cidr_block = "10.5.0.0/16"

  tags = {
    Name = "${local.staging_env}-subnet-tag"
  }
}

resource "aws_instance" "ec2_example" {
  ami           = "ami-0767046d1677be5a0"
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.staging-subnet.id

  tags = {
    Name = "${local.staging_env}-Terraform EC2"
  }
}
```

✔ Both files (with and without locals) work the same
✔ File with locals is cleaner, reusable, and better practice

---

## ⭐ Simple Summary

* Terraform locals are like local variables in programming.
* They help avoid repeating the same values in multiple places.
* Locals cannot change during runtime.
* They support both static and dynamic values.
* They make Terraform code cleaner, shorter, and easier to maintain.

---

## 🔥 2 Important Interview Q&A

### **1. What are locals in Terraform?**

Locals are reusable values defined inside a Terraform file.
They help avoid repeating the same values and make code easier to manage.

---

### **2. Why should we use locals in Terraform?**

To reduce duplication, improve readability, and make updates easier.
Instead of changing multiple places, you update the value once in the locals block.

---


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

Here is a **simple explanation**, **example**, **analogy**, and **interview Q&A** for **Terraform Modules**, written clean and easy to understand.

---

#  Terraform Modules 
A **Terraform Module** is a way to **organize**, **reuse**, and **group** Terraform code.
Instead of writing everything in one big `main.tf` file, we split the work into **separate folders** called modules.

Modules help when:

* Your Terraform code becomes large
* You want to reuse the same infrastructure pattern (EC2, VPC, RDS, etc.)
* You want clean and structured Terraform projects

---

## 🎯 Why Use Modules?

✔ Keep Terraform code clean
✔ Reuse code instead of repeating
✔ Easy to maintain
✔ Helps in large-scale real-time projects
✔ Reduces mistakes (write once, use many times)

---

## 🧩 Terraform Module Folder Structure (Simple Example)

```
project/
│── main.tf
│── variables.tf
│── outputs.tf
│── modules/
│     └── ec2/
│          ├── main.tf
│          ├── variables.tf
│          └── outputs.tf
```

---

## 🛠️ Example: EC2 Module

### **modules/ec2/main.tf**

```hcl
resource "aws_instance" "myec2" {
  ami           = var.ami
  instance_type = var.instance_type

  tags = {
    Name = var.ec2_name
  }
}
```

### **modules/ec2/variables.tf**

```hcl
variable "ami" {}
variable "instance_type" {}
variable "ec2_name" {}
```

---

##  Using the Module in Main Terraform File

### **main.tf**

```hcl
module "ec2_module" {
  source         = "./modules/ec2"
  ami            = "ami-0fc5d935ebf8bc3bc"
  instance_type  = "t2.micro"
  ec2_name       = "My-Server"
}
```

Terraform will now create the EC2 instance using the module.

---

## 🎨 Simple Analogy (Easy to Remember)

**Think of Terraform Modules like a "reusable recipe."**
If you have a recipe for making pizza 🍕:

* You don't rewrite it every time
* You just reuse the same recipe whenever you want

Similarly:
A **Terraform module is a reusable recipe for building infrastructure** (EC2, VPC, S3, etc.).

---

## ⭐ 2 Important Interview Q&A

### **1. What is a Terraform Module?**

A Terraform module is a reusable folder that contains Terraform configurations.
It helps to organize code and reuse the same infrastructure multiple times.

---

### **2. Why do we use modules in Terraform?**

To avoid repeating code, improve structure, and make it easy to maintain large Terraform projects.
Modules help reuse the same logic for EC2, VPC, RDS, S3, etc., like templates.

---


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

---


# Terraform State File 

The **Terraform state file** (`terraform.tfstate`) is one of the **most important parts** of Terraform.
It stores the **current status** of your infrastructure, so Terraform knows:

* What resources already exist
* What needs to be created
* What needs to be updated
* What needs to be deleted

Without the state file, Terraform has **no idea what is running** in your cloud environment.

---

## 🎯 What is Terraform State File?

* It is a **JSON file** created and managed by Terraform.
* It keeps track of **all resources** Terraform has created (EC2, VPC, IAM, S3, etc.).
* It stores **configuration details, metadata, dependencies**, and resource IDs.

---

## ⭐ Why State File is Important?

When you run:

* `terraform plan`
* `terraform apply`
* `terraform destroy`

Terraform **compares**:

1. Your configuration files (desired state)
2. The state file (current state)

Then Terraform decides what to **add, update, or delete**.

---

## 📌 1. Contents of the State File

State file contains:

* Resource attributes
* Dependencies
* Provider information
* Terraform version
* Metadata
* Sensitive information (public IP, private IP, access keys, etc.)

It is basically the **memory of Terraform**.

---

## 📌 2. Where is the State File Stored?

### **Local (Default)**

Terraform automatically creates:

```
terraform.tfstate
```

in the same folder as your Terraform files.

### **Remote (Recommended for Teams)**

For team environments, store the state file in a central location like:

* AWS S3
* Azure Storage
* Google Cloud Storage
* HashiCorp Consul

Remote state = safer + shared + versioned.

---

## 📌 3. Purpose of the State File

* Helps Terraform understand the **current infrastructure**
* Helps Terraform decide what to **create/update/delete**
* Without it, Terraform cannot track what already exists

**It maintains your infrastructure truth.**

---

## 📌 4. Concurrency & Locking

When multiple people run Terraform at the same time, there is a risk of corruption.

Terraform uses **state locking** (in remote backends like S3 + DynamoDB) to prevent:

* Conflicts
* Double changes
* Data corruption

Only **one Terraform process** can modify the state at a time.

---

## 📌 5. Remote State Backends

**Best practice** in companies = Store the state remotely.

Example:
AWS S3 (with DynamoDB lock table)

```hcl
terraform {
  backend "s3" {
    bucket = "thej-bucket"
    key    = "key/terraform.tfstate"
    region = "us-east-1"
  }
}
```

Terraform will store the `.tfstate` file in:

```
S3 bucket → key/terraform.tfstate
```

This allows:

✔ Collaboration
✔ Version control
✔ Safety
✔ Locking

---

## 📌 6. Sensitive Information Warning

State file may store:

* IP addresses
* Passwords
* Secrets
* Identifiers
* Private details

So **you must protect it**, especially in teams.

---

## 📌 7. Managing the State File

Terraform commands that work directly with the state file:

* `terraform apply`
* `terraform destroy`
* `terraform import`
* `terraform state list`
* `terraform state show`

Proper handling is important to avoid:

❌ Accidental deletion
❌ Broken resources
❌ Drift and mismatch

---

## 🎨 Simple Analogy (Easy to Remember)

Think of the **Terraform state file as a "to-do list + memory card"** for Terraform.

🧠 **Memory** —
Terraform remembers what it built by reading the state file.

📝 **To-do List** —
When you change your code, Terraform compares:

* Current memory (state file)
* Desired changes (your `.tf` files)

Then it decides what to add, update, or remove.

Without the state file, Terraform is **blind** and cannot manage your infrastructure.

---

## ⭐ Summary (Quick Notes)

* State file tracks everything Terraform has created.
* It is required for updates, deletions, and modifications.
* It contains sensitive information.
* Store locally for personal use.
* Store remotely (S3, Consul, Azure Storage) for teams.
* Locking prevents corruption.
* Essential for Terraform operations.

---

Here are the **Top 5 most important Terraform State File Interview Q&A** — short, clear, and perfect for real-time interviews:

---

## 🔥 **Top 5 Terraform State File – Interview Q&A**

---

### **1. What is the Terraform state file?**

The Terraform state file (`terraform.tfstate`) is a JSON file that stores the **current state of your infrastructure**.
Terraform uses it to understand what resources already exist and what changes are needed during `plan` or `apply`.

---

### **2. Why do we need a state file in Terraform?**

Terraform needs the state file to:

* Track resources it created
* Map real-world infrastructure to Terraform configs
* Decide what to add/update/destroy
* Avoid rebuilding already existing resources

Without the state file, Terraform **cannot manage** infrastructure properly.

---

### **3. What is the difference between local and remote state?**

| Local State                  | Remote State                                       |
| ---------------------------- | -------------------------------------------------- |
| Stored on your local machine | Stored in a cloud backend (S3, Azure, GCS, Consul) |
| Good for single developer    | Best for teams                                     |
| No locking                   | Supports locking to prevent conflicts              |
| Risk of loss/corruption      | Highly secure + versioning                         |

---

### **4. What is state locking and why is it important?**

State locking prevents **multiple users** from modifying the state file at the same time.
This avoids:

* Conflicts
* Duplicate changes
* Corrupted state

Example:
S3 backend with DynamoDB lock is a common enterprise setup.

---

### **5. What sensitive data can appear in a state file?**

State files may contain:

* Public/Private IPs
* Passwords
* Secrets
* Access tokens
* Resource metadata

Because of this, teams store tfstate securely using **remote encrypted storage**.

---

Here are **Top Real-Time Scenario-Based Terraform State Questions** — exactly what interviewers ask in DevOps rounds.
Short, practical, and perfect for 2–3 min answers.

---

## 🔥 **Top Real-Time Scenario-Based Terraform State Questions**

---

### **1. Scenario: Your teammate accidentally deleted the S3 Terraform state file. What will you do?**

**Answer:**

* Check if S3 bucket **versioning** is enabled.
* Restore the **previous version** of `terraform.tfstate`.
* If DynamoDB locking table was used, release the stale lock.
* Re-run `terraform refresh` or `terraform plan` to sync state.

> If versioning is not enabled, infrastructure may need to be imported again.

---

### **2. Scenario: Terraform is showing “state lock error”. What does it mean and how do you fix it?**

**Cause:**
Another Terraform process is holding the lock (or someone exited without unlocking).

**Fix:**

* For S3 + DynamoDB:

  * Go to DynamoDB → lock table → delete the **lock entry**
* Re-run the Terraform command
* Ensure locking is not disabled in backend config

---

### **3. Scenario: You manually created an EC2 instance in AWS. How will Terraform track it?**

**Answer:**
Use:

```bash
terraform import aws_instance.myec2 i-123abc456
```

This adds the existing EC2 instance into the **state file**.
Then run `terraform plan` to align configuration & state.

---

### **4. Scenario: Terraform plan wants to delete and recreate a resource, but you don’t want that. What will you do?**

**Options:**

1. Use **`terraform taint` (older versions only)**
2. Fix mismatch between tfstate and AWS → run `terraform refresh`
3. Modify your `.tf` code to match the real resource
4. Mark resource as **ignore changes**, e.g.:

```hcl
lifecycle {
  ignore_changes = [tags]
}
```

---

### **5. Scenario: Two people run `terraform apply` at the same time. What happens?**

**If using local state:**
❌ State gets corrupted
❌ Resources misbehave
❌ Unpredictable results

**If using remote state with locking:**
✔ Only one `terraform apply` runs
✔ The other person gets a “state locked” message
✔ No corruption

---

### **6. Scenario: Your local `terraform.tfstate` is out of sync with AWS. What will you do?**

**Fix methods:**

* Run:

```bash
terraform refresh
```

* Or run `terraform plan` to see drift
* Update code to reflect real infra
* Or import missing infra using `terraform import`

---

### **7. Scenario: You changed something manually in AWS (outside Terraform). What will Terraform do?**

Terraform will detect **drift** during `terraform plan`.
Your options:

* Update Terraform code
* Or let Terraform overwrite manual changes
* Or import the changed resource again

---

### **8. Scenario: You want different teams (dev, test, prod) to use separate state files. How do you handle it?**

Use **workspaces** or **different S3 keys**, like:

```hcl
key = "dev/terraform.tfstate"
key = "test/terraform.tfstate"
key = "prod/terraform.tfstate"
```

Or:

```bash
terraform workspace new dev
terraform workspace new prod
```

---

### **9. Scenario: Terraform state file contains secrets. How do you secure it?**

* Use **S3 with encryption (SSE-KMS)**
* Enable **bucket versioning**
* Enable **state locking (DynamoDB)**
* Restrict S3 bucket access with IAM
* Never store state on a developer laptop

---

### **10. Scenario: You need to move state from local → S3 backend. How do you do it?**

**Steps:**

1. Add backend block in `terraform {}`
2. Run:

```bash
terraform init
```

3. Terraform will ask:

```
Do you want to migrate state to the new backend? yes
```

✔ State will be automatically uploaded to S3.

---





# Terraform User Data

**User Data** in Terraform is used to **bootstrap (initialize)** an EC2 instance at the time of creation.
It allows you to install software, configure services, create files, or run any shell commands automatically when the instance launches.

Terraform does this using the **`user_data`** argument inside the `aws_instance` resource.

---

## ✅ Why User Data is Useful?

* Automatically install required applications (Apache, Nginx, Docker, etc.)
* Configure the server at boot time
* Avoid manual login and setup
* Good for **IaC + automation + reproducible deployments**

---

## ✅ Terraform `user_data` Syntax

```hcl
resource "aws_instance" "myec2" {
  ami           = "ami-0fc5d935ebf8bc3bc"
  instance_type = "t2.micro"

  # user_data block to install software on EC2 at launch
  user_data = <<-EOF
                #!/bin/bash
                sudo apt update -y 
                sudo apt install nginx -y
                systemctl enable nginx
                systemctl start nginx
              EOF
}
```

---

## 🔍 Key Points to Remember (Interview Ready)

* `user_data` runs **only once** at the first boot.
* Script must start with **`#!/bin/bash`**.
* Terraform sends this script to EC2 → EC2 cloud-init executes it.
* You can use:

  * `user_data` for inline script
  * `user_data_base64` for base64 encoded script
  * `templatefile()` to load large scripts from external `.sh` file

Example:

```hcl
user_data = templatefile("nginx-install.sh", {})
```

---

## ⭐ Interview Tip

If asked:
**“How do you automatically install applications on EC2 using Terraform?”**

Answer:
“By using the `user_data` block inside `aws_instance`, which passes a shell script executed by cloud-init at boot time. This helps fully automate server provisioning.”

---

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

Here is a **better, simple-English, interview-friendly version** of your topic **Terraform Variables (variable.tf)** with analogy + scenario-based Q&A.

---

#  Terraform Variables (variable.tf) 

In Terraform, a **variable file (variable.tf)** is used to **store all variables** that will be used inside `main.tf`.
Instead of hardcoding values directly in `main.tf`, you define them as variables and reference them using:

```
var.variable_name
```

This makes Terraform code:

✔ Cleaner
✔ Flexible
✔ Reusable
✔ Easier to maintain

In real-time projects, putting variables inside `main.tf` is **not recommended**.
Best practice → always use a **separate variable.tf file**.

---

## 📌 Example: Using variable.tf with main.tf

### **folder-1/main.tf**

```hcl
provider "aws" {
  region     = "eu-central-1"
  access_key = "<INSERT_YOUR_ACCESS_KEY>"
  secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {
  ami           = "ami-0767046d1677be5a0"
  instance_type = var.instance_type

  tags = {
    Name = "Terraform EC2"
  }
}
```

---

### **folder-1/variable.tf**

```hcl
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}
```

---

### **Run Terraform**

```bash
$ terraform init
$ terraform plan
$ terraform apply
```

✔ Terraform will read the variable from `variable.tf`
✔ and use it inside `main.tf`.

---

## 🎨 Simple Analogy (Easy to Remember)

Think of **variable.tf like a menu card** in a restaurant.

* The **menu (variable.tf)** contains item names and prices
* The **kitchen (main.tf)** cooks based on what you choose
* You don’t hardcode ingredients every time
* You just **pick from the menu**

Similarly:

* **variable.tf = stores and manages all variable values**
* **main.tf = uses those values to build resources**

---

## ⭐ Why Variables Are Important?

* Avoids hardcoding values
* Makes Terraform reusable
* Allows passing different values for dev, test, prod
* Helps cleanly organize configuration
* Allows overriding values during runtime (via CLI or tfvars file)

---

## 🔥 Real-Time Scenario-Based Q&A

---

## **1. Scenario: You need different EC2 instance types for dev, test, and prod. How will you handle it in Terraform?**

**Answer:**
Use variables and assign values through `*.tfvars` files.

Example:

`dev.tfvars`

```
instance_type = "t2.micro"
```

`prod.tfvars`

```
instance_type = "t3.medium"
```

Run:

```bash
terraform apply -var-file=prod.tfvars
```

This avoids modifying main.tf for each environment.

---

## **2. Scenario: Your team wants to hide sensitive values like passwords and keys. How will you do it?**

**Answer:**

Use:

```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

Or store secrets in:

* Environment variables
* SSM Parameter Store
* Secrets Manager
* tfvars files (never commit to Git)

Terraform will **mask** sensitive values in plan/apply output.

---

## **3. Scenario: A variable should not have a default value and user must provide it. How will you enforce it?**

**Answer:**
Do not set a default in variable.tf.

```hcl
variable "ami_id" {
  type        = string
  description = "AMI ID must be provided"
}
```

Terraform asks for input:

```
var.ami_id (Enter a value):
```

---

## ⭐ Summary

* `variable.tf` stores input variables
* `main.tf` uses them as `var.variable_name`
* Keeps Terraform clean, reusable, and flexible
* Perfect for multi-environment setups
* Essential for professional Terraform code structure

---


# Terraform `*.tfvars` — simple, correct, and practical

**What it is:**
A `*.tfvars` file holds the *actual values* for variables you declare in `variable` blocks (usually in `variables.tf`).
It keeps configuration separate from code: `main.tf` contains resource definitions, `variables.tf` declares inputs, and `terraform.tfvars` supplies the values.

---

## Key points (short)

* `terraform.tfvars` is **automatically loaded** by Terraform when present.
* You can also use any file name and pass it with `-var-file=<file>`.
* Do **not** commit secrets in tfvars — use environment variables, secret stores, or CI secrets.
* Variable precedence (highest → lowest): `-var` CLI, `TF_VAR_` env vars, `-var-file`, `terraform.tfvars`, variable defaults.

---

## Corrected example (folder-2)

Project layout:

```
folder-2/
  ├─ main.tf
  ├─ variables.tf
  └─ terraform.tfvars
```

### `main.tf`

```hcl
provider "aws" {
  region = "eu-central-1"
  # DO NOT hardcode long-lived secrets here in real projects
}

resource "aws_instance" "ec2_example" {
  ami           = "ami-0767046d1677be5a0"
  instance_type = var.instance_type

  tags = {
    Name = "Terraform EC2"
  }
}
```

### `variables.tf`

```hcl
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  # no default so the value must be provided (via tfvars, env, or CLI)
}
```

### `terraform.tfvars`

```hcl
instance_type = "t2.micro"
```

---

## Run these commands

```bash
cd folder-2
terraform init
terraform plan       # Terraform will pick values from terraform.tfvars automatically
terraform apply
```

Or explicitly with a var file:

```bash
terraform apply -var-file="custom.tfvars"
```

---

## Useful tips & best practices

* Use `terraform.tfvars` for non-sensitive environment-specific values (dev/test/prod).
* For secrets, use `TF_VAR_` env vars in CI or a secrets manager (AWS SSM/Secrets Manager).
* Use multiple tfvars files (e.g., `dev.tfvars`, `prod.tfvars`) and pass `-var-file` during CI/CD.
* Keep variable **types** (string, number, list, map) declared in `variables.tf` to catch input errors early.
* Prefer defaults for safe fallbacks, but omit defaults for required values you want to force providers to supply.

---

## Quick example: overriding for prod

```
terraform apply -var-file="prod.tfvars"
```

This allows one codebase to run for multiple environments by swapping tfvars.

---

Here are the **Top 5 quick interview Q&A about Terraform tfvars** — short, simple, and perfect for interviews:

---

## 🔥 **Top 5 Terraform tfvars Interview Q&A**

---

### **1. What is a tfvars file in Terraform?**

A `tfvars` file is used to provide **actual input values** for variables declared in `variables.tf`.
It keeps variable values separate from the main configuration.

---

### **2. Why do we use terraform.tfvars instead of putting values in variables.tf?**

Because `terraform.tfvars` allows:

* Easy environment separation (dev/stage/prod)
* Cleaner code structure
* Secure handling (no defaults for sensitive values)
* Reusable configurations

`variables.tf` only **declares** variables; the **actual values** belong in tfvars.

---

### **3. Does Terraform automatically load terraform.tfvars?**

Yes.
Terraform automatically loads the following:

* `terraform.tfvars`
* `terraform.tfvars.json`
* Any file named `*.auto.tfvars`

For other filenames, you must pass `-var-file`.

---

### **4. How do you use multiple tfvars files for different environments?**

Example:

```
dev.tfvars  
prod.tfvars  
```

Run:

```bash
terraform apply -var-file=prod.tfvars
```

This allows one Terraform codebase to manage multiple environments.

---

### **5. What is the difference between -var and -var-file?**

* `-var` → pass single variable

  ```bash
  terraform apply -var="instance_type=t2.micro"
  ```

* `-var-file` → load many variables from a file

  ```bash
  terraform apply -var-file="prod.tfvars"
  ```

`-var-file` is preferred for multiple or sensitive values.

---

## 🚀 Using Multiple Terraform `.tfvars` Files (Best Practice)

Terraform allows you to use **multiple `.tfvars` files** to manage **multiple environments** (dev, stage, prod) with **one single main.tf file**.
Each `.tfvars` file contains the *actual values* for variables declared in `variables.tf`.

✔ No need for default values in `variables.tf`
✔ One codebase, multiple environments
✔ Clean, flexible, and reusable

---

## 📁 Folder Structure (folder-3)

```
folder-3/
  ├── main.tf
  ├── variables.tf
  ├── stage.tfvars
  └── prod.tfvars
```

---

## 🧩 Step 1: `main.tf`

```hcl
provider "aws" {
  region     = "eu-central-1"
}

resource "aws_instance" "ec2_example" {
  ami           = "ami-0767046d1677be5a0"
  instance_type = var.instance_type

  tags = {
    Name = var.environment_name
  }
}
```

Here we are using two variables:

* `instance_type`
* `environment_name`

---

## 🧩 Step 2: `variables.tf`

```hcl
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
}

variable "environment_name" {
  description = "Name tag for the environment"
  type        = string
}
```

No default values → must be supplied via `.tfvars`

---

## 🧩 Step 3: Create different `.tfvars` files

---

## 🌱 **stage.tfvars** (for staging environment)

```hcl
instance_type   = "t2.micro"
environment_name = "stage"
```

---

## 🚀 **prod.tfvars** (for production environment)

```hcl
instance_type   = "t3.medium"
environment_name = "prod"
```

(You can change instance type based on production needs.)

---

## 🛠️ Step 4: Run Terraform for Each Environment

### For Staging:

```bash
terraform apply -var-file="stage.tfvars"
```

### For Production:

```bash
terraform apply -var-file="prod.tfvars"
```

---

## 🎯 Why Use Multiple tfvars Files?

* Separate configuration for dev/stage/prod
* No code duplication
* One `main.tf` works for all environments
* Easy environment switching using `-var-file`
* Avoids hardcoding values
* Cleaner and more professional Terraform setup

---

## 🎨 Simple Analogy (Easy to Remember)

Think of your Terraform code as a **single cake recipe** (main.tf).
The `.tfvars` files are **different sets of ingredients**:

* `stage.tfvars` → small cake
* `prod.tfvars` → big cake
* `dev.tfvars` → test cake

**One recipe, different ingredient sets → different outputs.**

---



# To supply different tfvars files in the same location 
```bash
$ terraform plan -var-file="stage.tfvars" # now only stage.tfvars files only reflected into main.tf file according to this machine name will come like stage as a name environment and machine will t2.micro 

$ terraform apply -vars-file="stage.tfvars"

$ terraform apply -var-file="prod.tfvars" # based on this only prod file will be refleted according to our use case 
```



Here is a **clean, simple-English, interview-ready version** of your topic **Terraform: Setting Variables from Command Line (-var)** — with improved formatting and corrections.

---

# Setting Variables Using Command Line (-var)

Terraform allows you to pass variable values **directly from the command line** without using:

* `terraform.tfvars`
* `*.tfvars` files
* default values in `variables.tf`

This is useful for:

✔ Quick testing
✔ One-time deployments
✔ Temporary overrides
✔ CI/CD pipelines

---

## 📁 Step 1: Create the folder

```bash
mkdir folder-4
cd folder-4
```

---

## 🧩 Step 2: Create `main.tf`

```hcl
provider "aws" {
  region = "eu-central-1"
}

resource "aws_instance" "ec2_example" {
  ami           = "ami-0767046d1677be5a0"
  instance_type = var.instance_type

  tags = {
    Name = "Terraform EC2"
  }
}
```

Here, `instance_type` is taken from a variable.

---

## 🧩 Step 3: Create `variables.tf`

```hcl
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
}
```

No default value → Terraform will **require** the user to provide a value.

---

## 🛠️ Step 4: Pass variable from command line

Run Terraform apply with `-var`:

```bash
terraform apply -var="instance_type=t2.micro"
```

Terraform will:

* Read variable definition from `variables.tf`
* Take the **actual value** from `-var`
* Create an EC2 instance with instance type **t2.micro**

---

## 🎯 Why use `-var`?

* Quick deployment
* No need to create tfvars file
* Perfect for CI/CD scripts
* Lets you override default values easily

Example:

```bash
terraform plan -var="instance_type=t3.medium"
```

---

## ⭐ Simple Analogy (Easy to Remember)

Think of `-var` like ordering food and telling the chef special instructions:

> “Make the same recipe, but use **t2.micro** instead of default.”

You are telling Terraform:

> “Use the same configuration, but replace the variable value with what I give in the command.”

---

## 📌 Notes

* `-var` works only for **single values**.
* For multiple values, it’s better to use `*.tfvars`.
* Sensitive values (passwords, secrets) should NOT be passed using `-var`, especially in CI logs.

Use environment variables instead:

```bash
export TF_VAR_password="mypassword123"
```

---

#  Terraform Workspaces (Simple Explanation)

Terraform **workspaces** allow you to use the **same Terraform code** (same `main.tf`) to create **multiple environments** such as:

* dev
* test
* stage
* prod

Each workspace has its **own separate state file**, so resources in one environment do **not** affect another.

---

## 🎯 Why Workspaces?

Using workspaces, you can:

* Use **one main.tf file** for all environments
* Keep state files **separated and isolated**
* Create resources for dev, test, prod without maintaining multiple folders
* Reduce code duplication

It is very useful when your infra is small or simple.

---

## 🎨 Simple Analogy (Easy to Remember)

Think of **Terraform workspaces like Git branches**:

* Git branches → separate code versions
* Terraform workspaces → separate environment states

Just like you can work on different features in different branches,
you can create **different environments** in different workspaces using the same code.

---

## 🛠️ Example: Using Workspaces to Create Dev & Test EC2 Instances

### `main.tf`

```hcl
provider "aws" {
  region = "eu-central-1"
}

resource "aws_instance" "ec2_example" {
  ami           = "ami-0767046d1677be5a0"
  instance_type = "t2.micro"

  tags = {
    Name = "EC2-${terraform.workspace}"
  }
}
```

In this example:

* When workspace = dev → instance name = `EC2-dev`
* When workspace = test → instance name = `EC2-test`

---

## 🧩 Commands for Workspaces

### **1. Create a new workspace**

```bash
terraform workspace new dev
```

This creates a new workspace AND switches to it.

---

### **2. List all workspaces**

```bash
terraform workspace list
```

---

### **3. Show active workspace**

```bash
terraform workspace show
```

---

### **4. Switch to another workspace**

```bash
terraform workspace select test
```

---

## 📝 Real-Time Workflow Example

### Step 1: Create **dev workspace** and deploy infra

```bash
terraform workspace new dev
terraform apply
```

Terraform creates **dev EC2 instance** (Name → EC2-dev)

---

### Step 2: Switch to **test workspace**

```bash
terraform workspace new test
terraform apply
```

Terraform creates **test EC2 instance** (Name → EC2-test)

---

✨ **Both environments use the same code but have different state files and different resources.**

---

## ⭐ Summary

* Workspaces = multiple environments from single Terraform code
* Each workspace has isolated state
* Very similar to Git branches
* Useful for small to medium infra setups
* `terraform.workspace` allows you to use workspace name inside configuration

---

