This is your first step.

## Task

This is Dan's _example_ of creating a scenario and running a **command**

`echo 'Hello World'`{{execute}}

We have provided [several sample architectures in GitHub](https://github.com/do-community/terraform-sample-digitalocean-architectures/tree/master/01-minimal-web-db-stack) that you can deploy using Terraform. This tutorial will guide you through how to deploy one of these architectures into a VPC network on your DigitalOcean account that includes three Droplets attached to database and a load balancer. The Droplets will be immediately accessible via the web.

{{< image "/vpc/resources/vpc-terraform-architecture.png" "The sample web application architecture." >}}

## Install Terraform

You need to create an [API token in your DigitalOcean account](https://www.digitalocean.com/docs/apis-clis/api/create-personal-access-token). This allows Terraform to access the DigitalOcean API and programmatically deploy resources into your account.

Next, you need to own a domain name and [have its DNS records hosted by DigitalOcean](https://www.digitalocean.com/docs/networking/dns/how-to/add-domains). The domain name will allow you to access the newly deployed Droplets via a hostname.

Lastly, you need to install Terraform on the machine you are deploying from. Terraform is available for MacOS, Windows, Linux, and FreeBSD.

To install Terraform on Ubuntu:

`sudo apt-get update`{{execute}}

You will need the `wget` and `unzip` utilities to download and extract the terraform files. If your system does not have the `wget` and `unzip`, use the following command to install them:

`sudo apt-get install wget unzip`{{execute}}

To download the Terraform files using `wget`:

`sudo wget https://releases.hashicorp.com/terraform/0.12.2/terraform_0.12.2_linux_amd64.zip`

Then extract the downloaded files using `unzip`. This will effectively install Terraform on your system:

`sudo unzip ./terraform_0.12.2_linux_amd64.zip`{{execute}}

Then set the path:

`sudo mv terraform /usr/local/bin/`{{execute}}

Once the files have been extracted, verify Terraform's installation:

`terraform version`{{execute}}

If installed correctly, the system should return Terraform's version information:

```shell
Terraform v.0.12.2
```

## Step 1: Download the DigitalOcean Sample Terraform GitHub Repo
We have set up a GitHub repository with several sample web application architectures that you can deploy into your account using Terraform. You can adjust the configuration files later on to suit your infrastructure's needs.

### How do I do this?
To download the repository to your system:

`git clone https://github.com/do-community/terraform-sample-digitalocean-architectures`{{execute}}

This becomes the directory you will run the Terraform commands from in the command line.

## Step 2: Configure Terraform Environment Variables
Before running Terraform commands, you need to define a few default variables for the Terraform environment. Terraform uses the variable values of two different types of files in its directory, `.tf` files and `.tfvars` files.

`.tf` files can define all specifications of a deployment, such as how many Droplets to create, their memory sizes, and which operating systems they will use.

`tfvars` files can define all or some aspects of a deployment, but should be considered as a set of environmental variables that Terraform falls back on as a default when a variable is left blank in an any `.tf` file. This is useful in applying variables that seldom change between deployments, such as API tokens or datacenter regions.

We have provided a sample `.tfvars` file in the directory that contains variables that are required for deployment into your DigitalOcean account, including:

* `do_token` - Your DigitalOcean API token.
* `ssh_key` - An SSH key to apply to your newly deployed Droplets.
* `subdomain` - The subdomain to apply to the Droplets and where they will be accessible from, such as the `www` of `www.example.com`.
* `domain_name` - The domain the Droplets will become accessible from.

### How do I do this?
1. Open the `⁨terraform-sample-digitalocean-architectures⁩\01-minimal-web-db-stack\⁩nyc3.tfvars` file in a text editor.

`vim terraform-sample-digitalocean-architectures⁩\01-minimal-web-db-stack\⁩nyc3.tfvars`{{execute}}

2. Edit the values of each field, replacing the `CHANGE_ME` values with your own.

The result should look something like this:

```
do_token = "4490a8331fed701f656f3074b8854f3x7fefaf4d474390065d0fba1081c7dbd"
region = "nyc3"
droplet_count = 3
ssh_key = "id_rsa.pub"
subdomain = "minimal-vpc"
domain_name = "do-example.com"
```

Once you've edited the values, save the changes to the file snd close it.

## Step 3: Configure Project Variables
Once you've configured Terraform's environmental variables, you can begin defining the variables of your deployment.

In the provided Terraform directory, we've defined several `.tf` files for each piece of the sample web architecture. You can modify each one to meet the needs of your architecture, but we have provided a `variables.tf` file with several rudimentary optional variables to edit, including:

* `name` - The name to prepend to the resources.
* `region` - The region to deploy the resources into.
* `droplet_count` - The number of Droplets to create.
* `db_count` - The number of databases to create.
* `droplet_size` - The amount memory and cores each Droplet will have.
* `database_size` - The databases' hard disk size.
* `image` - The operating system each Droplet will run.

Inside the `variables.tf` are two sections of variables, the aforementioned optional variables and a set of required variables. The required variables can be left blank and their values will default to the environmental variables during the deployment.

### How do I do this?
If you want to change the optional variables, open the `⁨terraform-sample-digitalocean-architectures⁩\01-minimal-web-db-stack\⁩variables.tf` file in a text editor, then edit the values of the `default` values in each section.

{{< prism language="yaml" title="/etc/netplan/50-cloud-init.yaml" highlightLines="5" >}}
}
# The number of database nodes to create
variable "db_count" {
    type = number
    default = 1
}
{{< /prism >}}

Once you've edited the values, save the changes to the file and save it.

## Step 4: Initialize, Review Plan, and Execute Terraform
Terraform requires three steps for deployment: initializing the directory, reviewing an execution plan, and applying (executing) the Terraform plan.

Initialization prepares the working directory for use by accounting for any changes in Terraform's backend configuration. The planning step allows you to review the resources that will be deployed before execution. Lastly, the `terraform apply "infra.out"` command executes the deployment of the resources into your account.

### How do I do this?

1. From inside the `01-minimal-web-db-stack` directory, run `terraform init` to initialize it.
2. To review your Terraform plan, run `terraform plan -var-file=YOUR_VARS.tfvars -out=infra.out`, replacing the `YOUR_VARS.tfvars` with your `.tfvars` file. Terraform saves the plan to an `infra.out` inside the working directory. Open the file in a text editor to review your Terraform plan.
3. If satisfied with the plan, run `terraform apply "infra.out"`.

Terraform will create the resources outlined in the Terraform plan into your DigitalOcean account.

## Summary {#tldr}
Once you've deployed resources into your account, you can use this same workflow to deploy similar architectures for other web applications into your account. You only need to complete these steps once:

* Create an API token.
* Download Terraform
* Set up environmental variables

To deploy additional architectures using the same configuration, run the following commands from the Terraform directory:

1. Run `terraform init` to initialize the directory.
2. Run `terraform plan -var-file=YOUR_VARS.tfvars -out=infra.out` to create and review your Terraform plan.
3. Review your Terraform plan by opening `infra.out` file in a text editor.
4. Run `terraform apply "infra.out"` to deploy resources.

## What's Next?
After deploying the web application infrastructure, you can use your new Droplets and network to host your websites and applications.
