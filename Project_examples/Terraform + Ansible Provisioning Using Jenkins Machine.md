

# 🚀 Terraform + Ansible Provisioning Using Jenkins Machine

This setup allows Jenkins to **provision EC2 using Terraform** and then **configure it using Ansible**.

---

# ✅ STEP 1: Setup Jenkins Machine

### **Install required tools**

* Install **Terraform**
* Install **Ansible**

---

## **1.1 Create SSH key for Jenkins user**

```bash
sudo su - jenkins
ssh-keygen -t ed25519
```

This creates two files inside `/var/lib/jenkins/.ssh/`:

* `id_ed25519` → private key
* `id_ed25519.pub` → public key

(**ed25519** is more secure than **rsa**)

---

## **1.2 Create directory for Terraform files & give permission**

Switch to **root** user:

```bash
mkdir -p /jenkins/jobs
chown -R jenkins:jenkins /jenkins
```

---

## **1.3 Create Terraform files inside Jenkins directory**

Switch back to Jenkins user:

```bash
sudo su - jenkins
cd /jenkins/jobs
mkdir terraform_work
cd terraform_work
```

Create Terraform files:

```bash
vim ec2.tf
vim variable.tf
```

Write your Terraform configuration inside these files.

---

## **1.4 Run Terraform commands manually**

Inside the directory:

```bash
terraform init
terraform plan
terraform apply
```

This confirms that Terraform works properly from Jenkins machine.

---

# ✅ STEP 2: Create Jenkins Job (Freestyle Project)

### Create job:

* Open Jenkins UI
* Click **New Item**
* Enter job name:
  **setup_ec2_machinen_with_apache**
* Select **Freestyle project**

---

## Add Description

```
This job sets up Apache using Terraform and Ansible.
```

---

## Add Build Step → Execute Shell

Paste the below script:

```bash
cd /jenkins/jobs/terraform_work

terraform init -input=false

terraform plan -out=tfplan -input=false

terraform apply -input=false tfplan
```

---

Click **Save**.

---

# ▶️ STEP 3: Build the Job

Click **Build Now** to execute Terraform from Jenkins.

Jenkins will:

* Initialize Terraform
* Create a plan
* Apply the plan
* Provision EC2 instance

---

If you want, I can give:
✅ Ansible integration step
✅ Jenkins pipeline version
✅ Complete Terraform + Ansible code example
