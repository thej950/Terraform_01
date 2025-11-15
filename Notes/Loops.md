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
