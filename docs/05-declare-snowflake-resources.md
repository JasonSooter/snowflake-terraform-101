# Declaring Resources

Add a file to your project in the base directory named main.tf. In main.tf we set up the provider and define the configuration for the database and the warehouse that we want Terraform to create.

Copy the contents of the following block to your main.tf

```tf
terraform {
  required_providers {
    snowflake = {
      source  = "Snowflake-Labs/snowflake"
      version = "~> 0.76"
    }
  }
}

provider "snowflake" {
  role = "SYSADMIN"
}

resource "snowflake_database" "db" {
  name = "TF_DEMO"
}

resource "snowflake_warehouse" "warehouse" {
  name           = "TF_DEMO"
  warehouse_size = "large"
  auto_suspend   = 60
}
```

This is all the code needed to create these resources.
