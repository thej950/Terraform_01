# Terraform Varaibles:

Terraform variables help you make your Terraform files flexible, reusable, and clean.
There are two main categories of variables:

**Types of variables:**

Terraform variables help you make your Terraform files **flexible**, **reusable**, and **clean**.
There are **two main categories** of variables:


## ✅ **1. Simple Value Variables**

Used to store **one single value**
Types:

* `string`
* `number`
* `bool`

---

## ✅ **2. Collection Variables**

Used to store **multiple values**
Types:

* `list`
* `map`
* `set`

---


# 🧩 Structure of a Terraform Variable Block

```hcl
variable "<variable_name>" {
  description = "Meaningful description of the variable"
  type        = string | number | bool | list | map | set
  default     = <default_value>
}
```

* `description` → What the variable is used for
* `type` → Value type
* `default` → Optional default value

If no default is given → Terraform **expects user input**

---


# Examples Variable Usage ( **string, number, bool** )

## 🔹 Example 1: String Type Variable

### **Without variables (hardcoded EC2)**

```hcl
provider "aws" {
  region     = "eu-central-1"
  access_key = "<ACCESS_KEY>"
  secret_key = "<SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {
  ami           = "ami-0767046d1677be5a0"
  instance_type = "t2.micro"

  tags = {
    Name = "Terraform EC2"
  }
}
```

---

### **With string variable (recommended)**

### **main.tf**

```hcl
provider "aws" {
  region     = "eu-central-1"
  access_key = "<ACCESS_KEY>"
  secret_key = "<SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {
  ami           = "ami-0767046d1677be5a0"
  instance_type = var.instance_type

  tags = {
    Name = "Terraform EC2"
  }
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}
```

✔ More flexible
✔ Easy to update instance type
✔ Good practice for production

---

## 🔹 Example 2: Number Type Variable

Number variables are used for:

* EC2 count
* Instance size values
* Port numbers
* CIDR block notation (when numeric)

### **Define number variable**

```hcl
variable "instance_count" {
  description = "Number of EC2 instances"
  type        = number
  default     = 2
}
```

### **Use number variable in main.tf**

```hcl
resource "aws_instance" "ec2_example" {
  ami           = "ami-0767046d1677be5a0"
  instance_type = "t2.micro"
  count         = var.instance_count

  tags = {
    Name = "Terraform EC2"
  }
}
```

Terraform will now create **2 EC2 instances**.

---

## 🔹 Example 3: Boolean Type Variable

Boolean (`true/false`) variables are used for:

* Enabling or disabling features
* Attaching public IP
* Toggling resources
* Conditional logic

### **Define boolean variable**

```hcl
variable "enable_public_ip" {
  description = "Enable public IP address"
  type        = bool
  default     = true
}
```

### **Use boolean variable**

```hcl
resource "aws_instance" "ec2_example" {
  ami           = "ami-0767046d1677be5a0"
  instance_type = "t2.micro"
  count         = 1

  associate_public_ip_address = var.enable_public_ip

  tags = {
    Name = "Terraform EC2"
  }
}
```

If `enable_public_ip = false` → no public IP will be attached.

---

## ⭐ Summary (Easy to Remember)

| Variable Type        | Purpose           | Example               |
| -------------------- | ----------------- | --------------------- |
| **string**           | single text value | AMI ID, instance type |
| **number**           | numeric values    | count, port number    |
| **bool**             | true/false        | enable_public_ip      |
| **list / map / set** | multiple values   | subnets, tags, AZs    |

---
		

Here is a **clean, improved, simple-English version** of your content — using **only the information you provided**, nothing extra.

---

# Examaples of Collection Types  ( **List, Set, Map** )

Terraform supports **collection-type variables** when you need to store **multiple values** inside a single variable.
The main collection types are:

1. **List** → ordered list of values
2. **Set** → unordered unique values (not used in your examples)
3. **Map** → key-value pairs

---

## ✅ **1. List Type Variables**

A **list** stores multiple values in a single variable.
Useful for multiple users, multiple subnets, multiple instance names, etc.

### **Syntax**

```hcl
variable "user_names" {
  description = "IAM usernames"
  type        = list(string)
  default     = ["user1", "user2", "user3"]
}
```

---

### **Using List Variable in main.tf**

```hcl
provider "aws" {
  region     = "eu-central-1"
  access_key = "<INSERT_YOUR_ACCESS_KEY>"
  secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {
  ami           = "ami-0767046d1677be5a0"
  instance_type = "t2.micro"
  count         = 1

  tags = {
    Name = "Terraform EC2"
  }
}

# Create multiple IAM users from list
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

✔ Output: Terraform will create **3 IAM users**.

---

### **Without list variable (single user)**

```hcl
resource "aws_iam_user" "iam-users" {
  name = "raju"
}
```

---

## ✅ **2. Map Type Variables**

A **map** stores **key–value pairs**.
Useful for passing environment details, tags, metadata, etc.

### **Syntax**

```hcl
variable "project_environment" {
  description = "project name and environment"
  type        = map(string)
  default     = {
    project     = "project-alpha",
    environment = "dev"
  }
}
```

---

### **Using Map Variable in main.tf**

```hcl
provider "aws" {
  region     = "eu-central-1"
  access_key = "<INSERT_YOUR_ACCESS_KEY>"
  secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {
  ami           = "ami-0767046d1677be5a0"
  instance_type = "t2.micro"

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

✔ Output: Instance will have tags:

```
project = project-alpha
environment = dev
```
---

## 🚀 Terraform Variables – **Set Type Variable**

A **set** is similar to a list, but it has two differences:

1. **Order does not matter**
2. **All values must be unique**

Use `set` when:

* You want unique values
* Order does not matter
* You do not need duplicates

Example use cases:

* Unique usernames
* Unique availability zones
* Unique CIDR blocks

---

## ✅ **Set Variable Syntax**

```hcl
variable "allowed_ports" {
  description = "Unique list of allowed ports"
  type        = set(number)
  default     = [22, 80, 443]
}
```

✔ The values **must be unique**
✔ Order does **not** matter

---

## 🧩 **Using Set Variable in main.tf**

Below example creates inbound rules for each port in a security group.

```hcl
provider "aws" {
  region     = "eu-central-1"
  access_key = "<INSERT_YOUR_ACCESS_KEY>"
  secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_security_group" "example_sg" {
  name = "example-sg"

  dynamic "ingress" {
    for_each = var.allowed_ports
    content {
      from_port   = ingress.value
      to_port     = ingress.value
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  }
}

variable "allowed_ports" {
  description = "Unique list of allowed ports"
  type        = set(number)
  default     = [22, 80, 443]
}
```

✔ This will create **3 inbound rules**:

* port 22
* port 80
* port 443

The order may vary, because **set is unordered**.

---

## ⭐ Summary

| Type     | Stores          | Unique?             | Ordered? | Good for                  |
| -------- | --------------- | ------------------- | -------- | ------------------------- |
| **list** | multiple values | no                  | yes      | usernames, instance types |
| **set**  | multiple values | yes                 | no       | ports, unique IDs         |
| **map**  | key-value pairs | keys must be unique | yes      | tags, metadata            |

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
