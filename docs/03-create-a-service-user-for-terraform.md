# Create a Service User for Terraform

We will now create a user account separate from your own that uses key-pair authentication. The reason this is required in this lab is due to the provider's limitations around caching credentials and the lack of support for 2FA. Service accounts and key pair are also how most CI/CD pipelines run Terraform.

## Create an RSA key for Authentication

This creates the private and public keys we use to authenticate the service account we will use for Terraform.

```bash
$ cd ~/.ssh
$ openssl genrsa 2048 | openssl pkcs8 -topk8 -inform PEM -out snowflake_tf_snow_key.p8 -nocrypt
$ openssl rsa -in snowflake_tf_snow_key.p8 -pubout -out snowflake_tf_snow_key.pub
```

## Create the User in Snowflake

Log in to the Snowflake console and create the user account by running the following command as the ACCOUNTADMIN role.

But first:

1. Copy the text contents of the ~/.ssh/snowflake_tf_snow_key.pub file, starting after the PUBLIC KEY header, and stopping just before the PUBLIC KEY footer.
2. Paste over the RSA_PUBLIC_KEY_HERE label (shown below).

Execute both of the following SQL statements to create the User and grant it access to the SYSADMIN and SECURITYADMIN roles needed for account management.

```sql
CREATE USER "tf-snow" RSA_PUBLIC_KEY='RSA_PUBLIC_KEY_HERE' DEFAULT_ROLE=PUBLIC MUST_CHANGE_PASSWORD=FALSE;

GRANT ROLE SYSADMIN TO USER "tf-snow";
GRANT ROLE SECURITYADMIN TO USER "tf-snow";
```

> We grant the user SYSADMIN and SECURITYADMIN privileges to keep the lab simple. An important security best practice, however, is to limit all user accounts to least-privilege access. In a production environment, this key should also be secured with a secrets management solution like Hashicorp Vault, Azure Key Vault, or AWS Secrets Manager.
