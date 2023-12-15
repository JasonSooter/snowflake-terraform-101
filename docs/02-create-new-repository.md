# Create a New Repository

[Create](https://github.com/new) a new repository to hold your Terraform project. I chose the name `snowflake-terraform-101` in this lab.

Run the following commands if you use the git command-line client replacing `YOUR_GITHUB_USERNAME` with your username:

```bash
$ mkdir snowflake-terraform-101 && cd snowflake-terraform-101
$ echo "# snowflake-terraform-101" >> README.md
$ git init
$ git add README.md
$ git commit -m "first commit"
$ git branch -M main
$ git remote add origin git@github.com:JasonSooter/snowflake-terraform-101.git
$ git push -u origin main
```

> Tip – If the commands don't work, verify that you can connect to GitHub using your SSH key, and that your git config is correct for your account.

You now have an empty repo that we will use in subsequent steps to Terraform your Snowflake account.
