# Terraform Varaibles:
1. Terraform variables are used to store values and re-used them whenever they required for multiple times also  To pass into aws resource section To provision 

## Types of variables:
- there are two types of varibales 

1. simple value variable
2. collection variables 

- Simple value type variables are used to store only single value those are like ( string, number, bool ) 
- Collection type varibales are used to store multile values in a single variable ( List, map, set ) 
```t
# To define variables inside main.tf file 

variables "<our_variable_name>" {
    description	= "Instance type t2.micro"  ---------> Meaning Fule Description of the variable 
    type		= string  ---------------------------> here type of the variables Ex: string, number, bool, list, set, map; 
    default		= "t2.micro" ------------------------> variable default value 
```
```t
# To Pass string type variable inside main.tf file 
# string type variable syntax:
variable "instance_type" {
    description = "Instance type t2.micro"
    type        = string
    default     = "t2.micro"
} 
```		
```t
# Below is the sample terraform file without using variables
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {
    
    ami           = "ami-0767046d1677be5a0"
    instance_type = "t2.micro"
    
    tags = {
            Name = "Terraform EC2"
    }
}
```
-----------------------------------------------------------
from above For defining variable block you need
description : Small or short description about the purpose of the variable
type : What type of variable it is going to be ex - string, bool, number ...
default : What would be the default value of the variable
```t
# To pass variables inside main.tf file 
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  var.instance_type

    tags = {
            Name = "Terraform EC2"
    }
}

variable "instance_type" {
    description = "Instance type t2.micro"
    type        = string
    default     = "t2.micro"
}
```


# To pass Number Type variables 
1. Number type variable used for example like suppose we want create multiple instances 
```t
# sytax of Number type variable 
variable "instance_count" {
    description = "EC2 instance count"
    type        = number
    default     = 2
}  
```
```t
# To use number type variable inside main.tf file 
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}

resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  "t2.micro"
    count = var.instance_count

    tags = {
            Name = "Terraform EC2"
    }
}

variable "instance_count" {
    description = "EC2 instance count"
    type        = number
    default     = 2
} 
```
		
# Boolean type variable:
- this bool variable are used to setup true or false values inside our terraform file 
```t
# syntax of bool variable in tf file 
variable "enable_public_ip" {
    description = "Enable public IP address"
    type        = bool
    default     = true
}
```
```t
# Below is the using bool varaible inside main.tf file  
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}


resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  "t2.micro"
    count = 1
    associate_public_ip_address = var.enable_public_ip

    tags = {
            Name = "Terraform EC2"
    }

}

variable "enable_public_ip" {
    description = "Enable public IP address"
    type        = bool
    default     = true
}
```

# Terraform variables - list, set, map 
 - when it comes to collection input variables 
1. list
2. set
3. map

## 1.  List type varaibles 
- list variables containe multiple values in a single format of variable 
```t
# Syntax of List type variable 
variable "user_names" {
description = "IAM usernames"
type        = list(string)
default     = ["user1", "user2", "user3s"]
}
```
```t
# Below are used List type varibale in main.tf file 
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
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
- from above main.tf file we are creating 3 IAM users 
```t
# To create a single user 
resource "aws_iam_user" "iam-users" {
    name = "raju"
}
```

## 2. 	Map variable type:
- terraform supports the map variable type where we can define the key-value pair 
- To use map varibales we can achive where we need to implement provosion in which environment suppose dev, test, prod environment like where we need to define project and environment, so we can use the map variable to achieve that 

```t
# Syntax of map varible to use in tf file 

variable "project_environment" {
    description = "project name and environment"
    type        = map(string)
    default     = {
    project     = "project-alpha",
    environment = "dev"
    }
}
```

```t
# To use map varibales in main.tf file 
provider "aws" {
    region     = "eu-central-1"
    access_key = "<INSERT_YOUR_ACCESS_KEY>"
    secret_key = "<INSERT_YOUR_SECRET_KEY>"
}
resource "aws_instance" "ec2_example" {

    ami           = "ami-0767046d1677be5a0"
    instance_type =  "t2.micro"

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

Here is a **better, simple-English, interview-friendly version** of your topic **Terraform Variables (variable.tf)** with analogy + scenario-based Q&A.

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
