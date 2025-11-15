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
