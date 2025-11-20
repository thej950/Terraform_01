# Terraform Provisioners: 
# 🚀 What Are Provisioners in Terraform?

Provisioners in Terraform are used to **run scripts or commands on a local machine or remote resource** *after* the resource is created.
They act like a **bootstrap step** for configuration.

✔ Example tasks:

* Installing software
* Running shell scripts
* Copying files
* Running Ansible/Chef/Puppet scripts
* Executing commands on EC2 after creation

---

# 🎯 Why Provisioners Exist?

Terraform mainly focuses on **infrastructure provisioning**.
But sometimes you need to run **custom commands** to configure a resource right after it is created.

Provisioners handle this *rare* but important need.

---

# 🧩 Types of Provisioners

### **1. local-exec**

Runs a command **on your local machine** (where Terraform is running).

Example:

```hcl
provisioner "local-exec" {
  command = "echo ${self.public_ip} >> hosts.txt"
}
```

---

### **2. remote-exec**

Runs commands **inside the remote VM** (SSH/WinRM).

Example:

```hcl
provisioner "remote-exec" {
  inline = [
    "sudo apt update",
    "sudo apt install nginx -y"
  ]
}
```

---

### **3. file provisioner**

Uploads a **local file** to the remote machine.

```hcl
provisioner "file" {
  source      = "app.sh"
  destination = "/tmp/app.sh"
}
```

---

# ⚠️ Important Notes

* **Provisioners are not recommended** unless absolutely necessary.
  Terraform docs say they are a **"last resort"**.
* You should use:

  * Cloud-init
  * User-data
  * Ansible
  * Chef
  * or other configuration tools
    instead of provisioners.

---

> Reference website: https://jhooq.com/terraform-provisioner/ 
