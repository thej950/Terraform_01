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
