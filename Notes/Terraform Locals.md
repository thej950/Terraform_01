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

