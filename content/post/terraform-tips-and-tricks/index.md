---
title: Terraform-Tips and Tricks
date: 2021-10-15T04:27:28.838Z
draft: false
featured: false
tags:
  - Terraform
categories:
  - Terraform
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
Terraform has a special command for visualizing the dependency graph: `terraform graph`. This command outputs a dotfile that can be converted to a digraph using a variety of tools.

For more verbose logs, you can turn on trace-level logging by setting the environment variable `TF_LOG=trace` to a non-zero value, e.g. `export TF_LOG=trace`.\
\
If terraform plan is running slowly, turn off trace-level logging and consider increasing parallelism (-parallelism=n).\
\
You should always run `terraform plan` before deploying. `terraform plan` informs you about what Terraform intends to do and acts as a linter, letting you know about any syntax or dependency errors. `terraform plan` is a read-only action that does not alter the state of deployed infrastructure, and like `terraform init`, it’s idempotent.\
\
Version lock any providers you use, whether they are implicitly or explicitly defined, to ensure that any deployment you make is repeatable.

Cleanup is done with a `terraform destroy `command

The major elements of Terraform are resources, data sources, and providers. **Data sources** are elements that allow you to fetch data at runtime and perform computations. Inputs are called **arguments**, and outputs are called **attributes**. There are also **computed attributes** that are only available after the resource has been created.\
\
Code blocks can be chained together to perform dynamic deployments.

All of the stateful information about the resource is stored in a file called `terraform.tfstate`.  Terraform reads the state file during a plan to decide what actions to take during an apply. It’s important not to lose the state file, or Terraform will lose track of all the resources it’s managing.\
\
The `terraform show` command can be used to print human-readable output from the state file and makes it easy to list information about the resources that Terraform manages.

You don’t want to pass secrets into the provider as plaintext, especially when this code will later be checked into version control, so many providers allow you to read secrets from environment variables or shared credential files.

Separating plan and apply like `terraform plan -out plan.out && terraform apply "plan.out"` is useful when running Terraform in automation. \
`terraform apply -auto-approve`. The optional `-auto-approve` flag tells Terraform to skip the manual approval step and immediately apply changes.

Each managed resource has life cycle function hooks associated with it: `Create(), Read(), Update(), and Delete().` Terraform invokes these function hooks as part of its normal operations

`terraform.tfstate.backup` is a copy of the previous state file and can be safely deleted if you wish.

`terraform refresh` is a read-only operation that does not modify managed existing infrastructure—just Terraform state.\
\
The Local provider for Terraform allows you to create and manage text files on your machine. This is normally used to glue together “real” infrastructure