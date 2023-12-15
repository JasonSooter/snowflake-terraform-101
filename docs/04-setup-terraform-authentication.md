# Setup Terraform Authentication

We need to pass provider information via environment variables and input variables so that Terraform can authenticate as the user.

Run the following to find the `YOUR_ACCOUNT_LOCATOR` and your Snowflake Region ID values needed.

```sql
SELECT current_account() as YOUR_ACCOUNT_LOCATOR, current_region() as YOUR_SNOWFLAKE_REGION_ID;
```

You can find your Region ID (`YOUR_REGION_HERE`) from `YOUR_SNOWFLAKE_REGION_ID` in this reference table.

Example: aws_us_west_2 would have a us-west-2 value for `YOUR_REGION_HERE`.

Add Account Information to Environment
Run these commands in your shell. Be sure to replace the `YOUR_ACCOUNT_LOCATOR` and `YOUR_REGION_HERE` placeholders with the correct values.

NOTE: Setting `SNOWFLAKE_REGION` is required if you are using a Legacy Account Locator.

```bash
$ export SNOWFLAKE_USER="tf-snow"
$ export SNOWFLAKE_AUTHENTICATOR=JWT
$ export SNOWFLAKE_PRIVATE_KEY=`cat ~/.ssh/snowflake_tf_snow_key.p8`
$ export SNOWFLAKE_ACCOUNT="YOUR_ACCOUNT_LOCATOR"
```

If you plan on working on this or other projects in multiple shells, it may be convenient to put this in a `snow.env` file that you can source or put it in your `.bashrc` or `.zshrc` file. For this lab, we expect you to run future Terraform commands in a shell with those set.
