# ViseFS: Dynamic User-Space Filesystem

---

# What is ViseFS?

<div align="center">

## 🚀 An Opinionated Dynamic User-Space Filesystem

**Built on FUSE with UniFuse for Portability**

</div>

**ViseFS ("Vise") lets you inject the same logical file or directory into multiple places**, present **synthetic structure**, and treat **generated/projected content as native on disk**.

**Key Idea**: Edit it in any injected location—there's a **single source of truth**.

---

# How It Works

<div align="center">

## 🔧 Core Mechanism

</div>

- **Multi-Injection Engine**: One canonical file/directory appears in multiple locations simultaneously
- **Synthetic Projection**: Present virtual structure without copying or symlinks
- **Config-Driven Injection**: Folder-local configs govern what materializes where
- **Layered Resolution**: Base → Patch → Generation → Injection stack

**Think**: A living virtual mount where code, tools, and artifacts appear exactly where needed—without duplication.

---

# Why Use ViseFS?

<div align="center">

## 💡 Problem It Solves

</div>

**Traditional filesystems force you to:**
- Copy files everywhere you need them
- Use symlinks that break across systems
- Manually manage versions and toolchains
- Deal with build artifact sprawl

**ViseFS makes filesystem composition effortless and lossless.**

---

# Illustrative Scenarios

<div align="center">

## 🌟 Real-World Use Cases

</div>

## Multi-Target Source Sharing
**One `shared/util/` visible at `pkg/a/util`, `pkg/b/util`, `tools/build/util`**

## Folder-Level Version Config
**`.vise.cfg` declares `tool.node = 18.x` or `20.x`; correct toolchain injects automatically**

## Toolchain Lattice
**Install N LLVM variants once; configs select the right one per folder**

## Build Ephemeral Outputs
**Transient artifacts at `build/bin/` backed by cache**

## Overlay Patching
**Virtual diffs over base trees for experiments**

## Generated Bindings
**`src/gen/{python,ts,rust}/` from one schema**

## Branch/Time Views
**Different commit trees without full clones**

## Template Expansion
**Instantiated skeletons with resolved variables**

---

# Core Themes

<div align="center">

## 🎯 Design Principles

</div>

1. **Multi-Presence Truth**: One origin, many appearances; edits unify
2. **Zero-Copy Projection**: No cloning or symlink gymnastics
3. **Config-Driven Injection**: Declarative folder-scoped selections
4. **Ephemeral Generation**: On-demand synthetic nodes with caching
5. **Deterministic Layering**: Predictable resolution stack
6. **High-Leverage Config**: Minimal API, maximal expressiveness
7. **Observability**: Inspect why nodes exist and their provenance

---

# Template Expansion

<div align="center">

## 📝 Template Expansion in Action

</div>

**ViseFS enables dynamic template expansion**: Present instantiated project skeletons with variables resolved, while the template and values remain the true source of truth.

**Real Example: Opening `main.tf` (Terraform Infrastructure)**

- **Normal Open** (`main.tf`): Shows the expanded Terraform config with variables resolved in real-time for the current environment.
- **Template Edit Mode** (`main.tf --edit-template`): Opens the underlying template file for editing infrastructure patterns.

**Filesystem Transformation Process**:

1. **File Open Request**: User opens `main.tf` in editor or via `terraform plan`.
2. **ViseFS Interception**: FUSE layer intercepts the open, checks for template context.
3. **Variable Resolution**: Fetches environment-specific variables from `terraform.tfvars` or config.
4. **Template Rendering**: Applies variables to template (`main.tf.template`) on-the-fly.
5. **Content Delivery**: Returns rendered Terraform code as if it's the native file.
6. **Real-Time Updates**: Changes to variables instantly update the infrastructure view on next access.

**Example Template** (`main.tf.template`):

```hcl
provider "aws" {
  region = "{{aws_region}}"
}

resource "aws_vpc" "main" {
  cidr_block = "{{vpc_cidr}}"
  tags = {
    Name = "{{project_name}}-vpc"
    Environment = "{{environment}}"
  }
}

resource "aws_instance" "web" {
  ami           = "{{ami_id}}"
  instance_type = "{{instance_type}}"
  subnet_id     = aws_subnet.public.id
  tags = {
    Name = "{{project_name}}-web"
  }
}
```

**Variables** (`terraform.tfvars`):
```hcl
aws_region     = "us-west-2"
vpc_cidr       = "10.0.0.0/16"
project_name   = "ecommerce-platform"
environment   = "production"
ami_id         = "ami-0c55b159cbfafe1d0"
instance_type  = "t3.large"
```

**Expanded Result** (what you see on normal open):
```hcl
provider "aws" {
  region = "us-west-2"
}

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "ecommerce-platform-vpc"
    Environment = "production"
  }
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1d0"
  instance_type = "t3.large"
  subnet_id     = aws_subnet.public.id
  tags = {
    Name = "ecommerce-platform-web"
  }
}
```

**Real-World Use Case**: **Multi-Environment Infrastructure Deployment**

Deploy the same infrastructure stack (VPCs, EC2 instances, databases) across dev, staging, and production environments. Each environment folder has its own variables file, injecting unique regions, instance sizes, and resource tags—without duplicating Terraform code.

**Why it matters**: Consistent infrastructure patterns, rapid environment setup, automated scaling, and easy updates to templates propagate across all deployments.

---

<div align="center">

# Questions?

**ViseFS: Coming Soon to a Filesystem Near You**

_Repository: [visefs/.github](https://github.com/visefs/.github)_

</div>
