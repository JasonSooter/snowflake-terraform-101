# Overview

Terraform is an open-source Infrastructure as Code (IaC) tool created by HashiCorp. It is a declarative Infrastructure as Code tool, meaning instead of writing step-by-step imperative instructions like with SQL, JavaScript or Python, you can declare what you want using a YAML like syntax. Terraform is also stateful, meaning it keeps track of your current state, and compares it with your desired state. A reconcilation process between the two states generates an plan that Terraform can execute to create new resources, or update/delete existing resources. This plan is implemented as an acyclic graph, and is what allows Terraform to understand and handle dependencies between resources. Using Terraform is a great way to manage account level Snowflake resources like Warehouses, Databases, Schemas, Tables, and Roles/Grants, among many other use cases.

A Terraform provider is available for Snowflake, that allows Terraform to integrate with Snowflake.

Example Terraform use-cases:

- Set up storage in your cloud provider and add it to Snowflake as an external stage
- Add storage and connect it to Snowpipe
- Create a service user and push the key into the secrets manager of your choice, or rotate keys

Many Snowflake customers use Terraform to comply with security controls, maintain consistency, and support similar engineering workflows for infrastructure at scale.

This tutorial demonstrates how you can use Terraform to manage your Snowflake configurations in an automated and source-controlled way. We show you how to install and use Terraform to create and manage your Snowflake environment, including how to create a database, schema, warehouse, multiple roles, and a service user.

This introductory tutorial does not cover specific cloud providers or how to integrate Terraform into specific CI/CD tools. Those topics may be covered in future labs.

## Prerequisites

- Familiarity with Git, Snowflake, and Snowflake objects
- `ACCOUNTADMIN` role access to a Snowflake account

## What You'll Learn

- Basic Terraform usage
- How to create users, roles, databases, schemas, and warehouses in Terraform
- How to manage objects from code/source control

## What You'll Need

- A GitHub account
- A git command-line client
- Text editor of your choice
- Terraform installed

## What You'll Build

- A repository containing a Terraform project that manages Snowflake account objects through code
