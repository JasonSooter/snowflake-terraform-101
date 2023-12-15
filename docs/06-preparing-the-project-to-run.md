# Preparing the Project to Run

To set up the project to run Terraform, you first need to initialize the project.

Run the following from a shell in your project folder:

```bash
$ terraform init
```

The dependencies needed to run Terraform are downloaded to your computer.

In this demo, we use a local backend for Terraform, which means that state file are stored locally on the machine running Terraform. Because Terraform state files are required to calculate all changes, its critical that you do not lose this file (`*.tfstate`, and `*.tfstate.*` for older state files), or Terraform will lose track of what resources it is managing. For any kind of multi-tenancy or automation workflows, it is highly recommended to use [Remote Backends](https://www.terraform.io/docs/language/state/remote.html), which is outside the scope of this lab.

The `.terraform` folder is where all provider and modules depenencies are downloaded. There is nothing important in this folder and it is safe to delete. You should add this folder, and the Terraform state files (since you do not want to be checking in sensitive information to VCS) to `.gitignore`.

Create a file named `.gitignore` in your project root, then add the following text to the file and save it:

```bash
_.terraform_
_.tfstate
_.tfstate.\*
```
