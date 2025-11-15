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

