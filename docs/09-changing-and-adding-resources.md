# Changing and Adding Resources

All databases need a schema to store tables, so we'll add that and a service user so that our application/client can connect to the database and schema. The syntax is very similar to the database and warehouse you already created. By now you have learned everything you need to know to create and update resources in Snowflake. We'll also add privileges so the service role/user can use the database and schema.

You'll see that most of this is what you would expect. The only new part is creating the private key. Because the Terraform TLS private key generator doesn't export the public key in a format that the Terraform provider can consume, some string manipulations are needed.

1. Change the warehouse size in the `main.tf` file from `xsmall` to `small`.
2. Add the following resources to your `main.tf` file:

```tf
provider "snowflake" {
  alias = "security_admin"
  role  = "SECURITYADMIN"
}

resource "snowflake_role" "role" {
  provider = snowflake.security_admin
  name     = "TF_DEMO_SVC_ROLE"
}

resource "snowflake_grant_privileges_to_role" "database_grant" {
  provider   = snowflake.security_admin
  privileges = ["USAGE"]
  role_name  = snowflake_role.role.name
  on_account_object {
    object_type = "DATABASE"
    object_name = snowflake_database.db.name
  }
}

resource "snowflake_schema" "schema" {
  database   = snowflake_database.db.name
  name       = "TF_DEMO"
  is_managed = false
}

resource "snowflake_grant_privileges_to_role" "schema_grant" {
  provider   = snowflake.security_admin
  privileges = ["USAGE"]
  role_name  = snowflake_role.role.name
  on_schema {
    schema_name = "\"${snowflake_database.db.name}\".\"${snowflake_schema.schema.name}\""
  }
}

resource "snowflake_grant_privileges_to_role" "warehouse_grant" {
  provider   = snowflake.security_admin
  privileges = ["USAGE"]
  role_name  = snowflake_role.role.name
  on_account_object {
    object_type = "WAREHOUSE"
    object_name = snowflake_warehouse.warehouse.name
  }
}

resource "tls_private_key" "svc_key" {
  algorithm = "RSA"
  rsa_bits  = 2048
}

resource "snowflake_user" "user" {
    provider          = snowflake.security_admin
    name              = "tf_demo_user"
    default_warehouse = snowflake_warehouse.warehouse.name
    default_role      = snowflake_role.role.name
    default_namespace = "${snowflake_database.db.name}.${snowflake_schema.schema.name}"
    rsa_public_key    = substr(tls_private_key.svc_key.public_key_pem, 27, 398)
}

resource "snowflake_grant_privileges_to_role" "user_grant" {
  provider   = snowflake.security_admin
  privileges = ["MONITOR"]
  role_name  = snowflake_role.role.name
  on_account_object {
    object_type = "USER"
    object_name = snowflake_user.user.name
  }
}

resource "snowflake_role_grants" "grants" {
  provider  = snowflake.security_admin
  role_name = snowflake_role.role.name
  users     = [snowflake_user.user.name]
}
```

To get the public and private key information for the application, use Terraform output values.Add the following resources to a new file named outputs.tf

```tf
output "snowflake_svc_public_key" {
  value = tls_private_key.svc_key.public_key_pem
}

output "snowflake_svc_private_key" {
  value = tls_private_key.svc_key.private_key_pem
  sensitive = true
}
```

The output from running `terraform apply`:

```bash
snowflake_database.db: Refreshing state... [id=TF_DB]
snowflake_warehouse.warehouse: Refreshing state... [id=TF_DEMO]

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create
  ~ update in-place

Terraform will perform the following actions:

  # snowflake_grant_privileges_to_role.database_grant will be created
  + resource "snowflake_grant_privileges_to_role" "database_grant" {
      + all_privileges    = false
      + id                = (known after apply)
      + on_account        = false
      + privileges        = [
          + "USAGE",
        ]
      + role_name         = "TF_DEMO_SVC_ROLE"
      + with_grant_option = false

      + on_account_object {
          + object_name = "TF_DB"
          + object_type = "DATABASE"
        }
    }

  # snowflake_grant_privileges_to_role.schema_grant will be created
  + resource "snowflake_grant_privileges_to_role" "schema_grant" {
      + all_privileges    = false
      + id                = (known after apply)
      + on_account        = false
      + privileges        = [
          + "USAGE",
        ]
      + role_name         = "TF_DEMO_SVC_ROLE"
      + with_grant_option = false

      + on_schema {
          + schema_name = "\"TF_DB\".\"TF_DEMO\""
        }
    }

  # snowflake_grant_privileges_to_role.user_grant will be created
  + resource "snowflake_grant_privileges_to_role" "user_grant" {
      + all_privileges    = false
      + id                = (known after apply)
      + on_account        = false
      + privileges        = [
          + "MONITOR",
        ]
      + role_name         = "TF_DEMO_SVC_ROLE"
      + with_grant_option = false

      + on_account_object {
          + object_name = (sensitive value)
          + object_type = "USER"
        }
    }

  # snowflake_grant_privileges_to_role.warehouse_grant will be created
  + resource "snowflake_grant_privileges_to_role" "warehouse_grant" {
      + all_privileges    = false
      + id                = (known after apply)
      + on_account        = false
      + privileges        = [
          + "USAGE",
        ]
      + role_name         = "TF_DEMO_SVC_ROLE"
      + with_grant_option = false

      + on_account_object {
          + object_name = "TF_DEMO"
          + object_type = "WAREHOUSE"
        }
    }

  # snowflake_role.role will be created
  + resource "snowflake_role" "role" {
      + id   = (known after apply)
      + name = "TF_DEMO_SVC_ROLE"
    }

  # snowflake_role_grants.grants will be created
  + resource "snowflake_role_grants" "grants" {
      + enable_multiple_grants = false
      + id                     = (known after apply)
      + role_name              = "TF_DEMO_SVC_ROLE"
      + users                  = (sensitive value)
    }

  # snowflake_schema.schema will be created
  + resource "snowflake_schema" "schema" {
      + data_retention_days = 1
      + database            = "TF_DB"
      + id                  = (known after apply)
      + is_managed          = false
      + is_transient        = false
      + name                = "TF_DEMO"
    }

  # snowflake_user.user will be created
  + resource "snowflake_user" "user" {
      + default_namespace  = "TF_DB.TF_DEMO"
      + default_role       = "TF_DEMO_SVC_ROLE"
      + default_warehouse  = "TF_DEMO"
      + disabled           = (known after apply)
      + display_name       = (sensitive value)
      + has_rsa_public_key = (known after apply)
      + id                 = (known after apply)
      + login_name         = (known after apply)
      + name               = (sensitive value)
      + rsa_public_key     = (known after apply)
    }

  # snowflake_warehouse.warehouse will be updated in-place
  ~ resource "snowflake_warehouse" "warehouse" {
        id                                  = "TF_DEMO"
        name                                = "TF_DEMO"
      ~ warehouse_size                      = "LARGE" -> "small"
        # (12 unchanged attributes hidden)
    }

  # tls_private_key.svc_key will be created
  + resource "tls_private_key" "svc_key" {
      + algorithm                     = "RSA"
      + ecdsa_curve                   = "P224"
      + id                            = (known after apply)
      + private_key_openssh           = (sensitive value)
      + private_key_pem               = (sensitive value)
      + private_key_pem_pkcs8         = (sensitive value)
      + public_key_fingerprint_md5    = (known after apply)
      + public_key_fingerprint_sha256 = (known after apply)
      + public_key_openssh            = (known after apply)
      + public_key_pem                = (known after apply)
      + rsa_bits                      = 2048
    }

Plan: 9 to add, 1 to change, 0 to destroy.

Changes to Outputs:
  + snowflake_svc_private_key = (sensitive value)
  + snowflake_svc_public_key  = (known after apply)

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

tls_private_key.svc_key: Creating...
tls_private_key.svc_key: Creation complete after 1s [id=dfc06d3acd426542dd185b0bb574628d85322613]
snowflake_role.role: Creating...
snowflake_schema.schema: Creating...
snowflake_warehouse.warehouse: Modifying... [id=TF_DEMO]
snowflake_role.role: Creation complete after 0s [id=TF_DEMO_SVC_ROLE]
snowflake_grant_privileges_to_role.database_grant: Creating...
snowflake_warehouse.warehouse: Modifications complete after 1s [id=TF_DEMO]
snowflake_grant_privileges_to_role.database_grant: Creation complete after 1s [id=TF_DEMO_SVC_ROLE|USAGE|false|false|false|true|false|false|false|false|DATABASE|TF_DB||false||false|]
snowflake_grant_privileges_to_role.warehouse_grant: Creating...
snowflake_schema.schema: Creation complete after 1s [id=TF_DB|TF_DEMO]
snowflake_user.user: Creating...
snowflake_grant_privileges_to_role.schema_grant: Creating...
snowflake_grant_privileges_to_role.warehouse_grant: Creation complete after 0s [id=TF_DEMO_SVC_ROLE|USAGE|false|false|false|true|false|false|false|false|WAREHOUSE|TF_DEMO||false||false|]
snowflake_grant_privileges_to_role.schema_grant: Creation complete after 0s [id=TF_DEMO_SVC_ROLE|USAGE|false|false|false|false|true|false|false|false||||false|"TF_DB"."TF_DEMO"|false|]
snowflake_user.user: Creation complete after 0s [id=tf_demo_user]
snowflake_role_grants.grants: Creating...
snowflake_grant_privileges_to_role.user_grant: Creating...
snowflake_grant_privileges_to_role.user_grant: Creation complete after 0s [id=TF_DEMO_SVC_ROLE|MONITOR|false|false|false|true|false|false|false|false|USER|tf_demo_user||false||false|]
snowflake_role_grants.grants: Creation complete after 1s [id=TF_DEMO_SVC_ROLE||tf_demo_user]

Apply complete! Resources: 9 added, 1 changed, 0 destroyed.

Outputs:

snowflake_svc_private_key = <sensitive>
snowflake_svc_public_key = <<EOT
-----BEGIN PUBLIC KEY-----
<PUBLIC_KEY_REDACTED>
-----END PUBLIC KEY-----

EOT
```
