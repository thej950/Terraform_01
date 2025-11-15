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

