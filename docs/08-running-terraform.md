# Running Terraform

Now that you have reviewed the plan, we simulate the next step of the CI/CD job by applying those changes to your account.

From a shell in your project folder (with your Account Information in environment) run:

```bash
$ terraform apply

Terraform used the selected providers to generate the following execution
plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # snowflake_database.db will be created
  + resource "snowflake_database" "db" {
      + data_retention_time_in_days = 1
      + id                          = (known after apply)
      + is_transient                = false
      + name                        = "TF_DB"
    }

  # snowflake_warehouse.warehouse will be created
  + resource "snowflake_warehouse" "warehouse" {
      + auto_resume                         = (known after apply)
      + auto_suspend                        = 60
      + enable_query_acceleration           = false
      + id                                  = (known after apply)
      + max_cluster_count                   = (known after apply)
      + max_concurrency_level               = 8
      + min_cluster_count                   = (known after apply)
      + name                                = "TF_DEMO"
      + query_acceleration_max_scale_factor = 8
      + resource_monitor                    = (known after apply)
      + scaling_policy                      = (known after apply)
      + statement_queued_timeout_in_seconds = 0
      + statement_timeout_in_seconds        = 172800
      + warehouse_size                      = "large"
      + warehouse_type                      = "STANDARD"
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

snowflake_database.db: Creating...
snowflake_warehouse.warehouse: Creating...
snowflake_database.db: Creation complete after 0s [id=TF_DB]
snowflake_warehouse.warehouse: Creation complete after 1s [id=TF_DEMO]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

Terraform regenerates the execution plan (unless you supply an optional path to the plan.out file) and applies the needed changes after confirmation. In this case, Terraform will create two new resources, and have no other changes.

Log in to your Snowflake account and verify that Terraform created the database and the warehouse.
