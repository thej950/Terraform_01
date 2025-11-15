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
