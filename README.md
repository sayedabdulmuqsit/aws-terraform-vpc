<div align="center">

# 🌐 AWS VPC — Infrastructure as Code with Terraform

**A production-style, version-controlled network foundation on AWS, built entirely with Terraform.**

![Terraform](https://img.shields.io/badge/Terraform-1.x-844FBA?style=flat-square&logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-eu--north--1-FF9900?style=flat-square&logo=amazonaws&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-success?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)

</div>

---

## 📖 Overview

This project provisions a **custom Virtual Private Cloud (VPC)** on AWS using Terraform — replacing manual, click-driven network setup with **declarative, repeatable, version-controlled infrastructure**.

Instead of configuring subnets and route tables by hand in the AWS Console, this repo defines the entire network topology as code — meaning the exact same infrastructure can be destroyed and recreated identically, anywhere, anytime, with three commands.

> Built as part of a hands-on cloud engineering roadmap — moving from manual AWS console operations to full Infrastructure as Code practices.

---

## 🏗️ Architecture

```
                          ┌─────────────────────────────┐
                          │      Internet Gateway        │
                          │      (terraform-igw)         │
                          └───────────────┬───────────────┘
                                          │
                          ┌───────────────▼───────────────┐
                          │         VPC (10.0.0.0/16)      │
                          │         terraform-vpc          │
                          │                                │
        ┌─────────────────┼────────────────┐               │
        │                 │                │               │
┌───────▼────────┐  ┌─────▼──────┐  ┌──────▼───────┐        │
│ Public Subnet   │  │ Route Table│  │ Private Subnet│       │
│ 10.0.1.0/24     │──│  (public)  │  │ 10.0.2.0/24   │       │
│ Auto-assign IP  │  │ 0.0.0.0/0  │  │ No public route│      │
└─────────────────┘  │  → IGW     │  └───────────────┘       │
                      └────────────┘                         │
                          └───────────────────────────────────┘
```

| Layer | Resource | CIDR | Internet Access |
|---|---|---|---|
| Network | VPC | `10.0.0.0/16` | — |
| Public tier | Public Subnet | `10.0.1.0/24` | ✅ via Internet Gateway |
| Private tier | Private Subnet | `10.0.2.0/24` | ❌ isolated |

---

## ⚙️ What Gets Provisioned

- ✅ 1× **VPC** with DNS support and DNS hostnames enabled
- ✅ 1× **Public Subnet** with auto-assign public IP
- ✅ 1× **Private Subnet** (isolated, no direct internet route)
- ✅ 1× **Internet Gateway** attached to the VPC
- ✅ 1× **Public Route Table** routing `0.0.0.0/0` → Internet Gateway
- ✅ 1× **Route Table Association** binding the public subnet to its route table

**6 resources. 0 manual clicks. Fully reproducible.**

---

## 🧰 Tech Stack

| Tool | Purpose |
|---|---|
| [Terraform](https://www.terraform.io/) | Infrastructure as Code engine |
| [AWS Provider (HashiCorp)](https://registry.terraform.io/providers/hashicorp/aws/latest) | Provisions real AWS resources |
| AWS VPC | Underlying networking service |
| Region: `eu-north-1` (Stockholm) | Deployment region |

---

## 📂 Project Structure

```
aws-terraform-vpc/
├── main.tf              # Core resources — VPC, subnets, IGW, route table
├── variables.tf         # Configurable inputs (CIDR blocks, region, AZ)
├── outputs.tf           # Exposed resource IDs after apply
├── .terraform.lock.hcl  # Locked provider versions for consistency
└── .gitignore           # Excludes state files & local cache
```

---

## 🚀 Usage

**Prerequisites:** [Terraform installed](https://developer.hashicorp.com/terraform/install) · AWS CLI configured with valid credentials

```bash
# 1. Initialize Terraform & download the AWS provider
terraform init

# 2. Preview exactly what will be created — zero surprises
terraform plan

# 3. Apply — provision the real infrastructure on AWS
terraform apply

# 4. Tear down — destroy everything cleanly, no leftover cost
terraform destroy
```

Each `apply` outputs the live resource IDs:

```
vpc_id            = "vpc-xxxxxxxxxxxxxxxxx"
public_subnet_id  = "subnet-xxxxxxxxxxxxxxxxx"
private_subnet_id = "subnet-xxxxxxxxxxxxxxxxx"
```

---

## 🔐 Security Notes

- State files (`.tfstate`) are **excluded from version control** — they can contain sensitive resource metadata and should never be committed to a public repo.
- No hardcoded credentials — AWS auth is handled entirely via the AWS CLI provider chain, never inline in code.
- Private subnet has **no route to the internet by design** — a real isolation boundary, not just a naming convention.

---

## 🎯 Why This Project Matters

Manually clicking through the AWS Console to build a VPC works — once. It doesn't scale, doesn't version, and doesn't survive a mistake. This project demonstrates:

- **Idempotency** — running `apply` twice changes nothing if nothing changed
- **Reproducibility** — the same `.tf` files build an identical network every time
- **Safe teardown** — `destroy` removes exactly what was created, nothing more, nothing less
- **Real IaC discipline** — the same practice used in production cloud teams

---

<div align="center">

**Built by [Abdul Muqsit Sayed](https://github.com/sayedabdulmuqsit)**  
*Part of a self-driven cloud engineering roadmap toward AWS Solutions Architect certification.*

</div>
