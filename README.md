
🚀 Auth0 Terraform Import and Production Tenant Setup
🧠 Overview

This guide walks you through managing Auth0 tenants with Terraform, importing existing manually created resources (like dev/stage tenants), and preparing a production tenant from scratch using infrastructure as code.

You’ll learn:

✅ How Terraform communicates with Auth0

⚙️ How to configure the provider

🧩 How to import existing Auth0 apps, APIs, and connections

🏗️ How to replicate your environment into production cleanly

🎯 How to confirm your migration is successful

🧱 1. Prerequisites

Before starting, ensure you have:

Terraform installed (terraform -v)

Access to Auth0 dashboard for all tenants (Dev, Stage, and new Prod)

Permission to create Machine-to-Machine (M2M) applications

Admin rights to Auth0 Management API

🌐 2. Understanding the Setup

Each environment (tenant) — dev, stage, prod — exists independently in Auth0.

Terraform can manage each tenant separately by configuring the Auth0 provider using credentials (client ID, secret, and domain) from an M2M app authorized to access the Auth0 Management API.

Why M2M?

Terraform uses Auth0’s Management API to:

Read and create clients (apps)

Manage APIs (resource servers)

Manage connections, users, roles, etc.

It needs API-level credentials, which come from an M2M app.

🔑 3. Create a Machine-to-Machine Application (for Terraform)

You’ll do this once per tenant (dev, stage, prod).

Steps:

In the Auth0 Dashboard → Go to Applications → Applications → Create Application

Choose:

Name: terraform-admin

Type: Machine to Machine Application

Click Create

Under API Access, choose Auth0 Management API

Grant the following permissions (minimum needed):

read:clients
create:clients
update:clients
delete:clients
read:connections
create:connections
update:connections
delete:connections
read:resource_servers
create:resource_servers
update:resource_servers
delete:resource_servers
read:rules
create:rules
update:rules
delete:rules


Click Authorize

Copy the following values from your app’s settings:

Client ID

Client Secret

Domain (example: dev-abc123.us.auth0.com)

⚙️ 4. Terraform Project Structure

Example directory:

auth0-terraform/
├── main.tf
├── provider.tf
├── variables.tf
├── terraform.tfvars
└── README.md

🧩 5. Configure Provider

provider.tf

terraform {
  required_providers {
    auth0 = {
      source  = "auth0/auth0"
      version = "~> 1.0"
    }
  }
}

provider "auth0" {
  domain        = var.auth0_domain
  client_id     = var.auth0_client_id
  client_secret = var.auth0_client_secret
}


variables.tf

variable "auth0_domain" {}
variable "auth0_client_id" {}
variable "auth0_client_secret" {}


terraform.tfvars

auth0_domain        = "dev-abc123.us.auth0.com"
auth0_client_id     = "YOUR_CLIENT_ID"
auth0_client_secret = "YOUR_CLIENT_SECRET"

🏗️ 6. Create Resource Skeletons

Before importing, you need to define dummy resources that match what you already have in Auth0.

Example (for a web app, API, and DB connection):

resource "auth0_client" "default_app" {
  name = "My Web App"
}

resource "auth0_resource_server" "api_server" {
  name = "My API"
  identifier = "https://myapi.example.com"
}

resource "auth0_connection" "db_connection" {
  name = "Username-Password-Authentication"
}


⚠️ These are placeholders — Terraform just needs to “know” the type and name before importing.

🔁 7. Import Existing Resources from Dev/Stage

Now, let’s import what’s already in your Auth0 tenant.

Step 1: Initialize Terraform
terraform init

Step 2: Import Each Resource

You’ll need to run one import command per existing resource.

Example:
Resource Type	Terraform Name	ID Source
Application (client)	auth0_client.default_app	Found in Applications → Settings → Client ID
API (resource server)	auth0_resource_server.api_server	Found in APIs → Settings → API ID
Database Connection	auth0_connection.db_connection	Found in Authentication → Database → Connection Name

Run:

terraform import auth0_client.default_app nKOqzkFxgHkhYu5PyJWJnXlpxgYM7byC
terraform import auth0_resource_server.api_server 68f420a69642a3ee0c07248d
terraform import auth0_connection.db_connection Username-Password-Authentication


Terraform will now create .tfstate entries that map your real Auth0 resources to Terraform.

📥 8. Verify Imports

After importing all resources, run:

terraform plan


✅ You should see output like:

auth0_resource_server.api_server: Refreshing state... [id=68f420a69642a3ee0c07248d]
auth0_client.default_app: Refreshing state... [id=nKOqzkFxgHkhYu5PyJWJnXlpxgYM7byC]
auth0_connection.db_connection: Refreshing state... [id=Username-Password-Authentication]

No changes. Your infrastructure matches the configuration.


🎉 That means Terraform now fully manages your existing Auth0 tenant!

🌍 9. Creating the Production Tenant

Auth0 tenants can’t be created via Terraform — you’ll do this manually.

Steps:

Go to Auth0 Dashboard

Click your profile → Create Tenant

Name it something like: mycompany-prod

Region: same as dev/stage for consistency

Once created, repeat Step 3 to create a new terraform-admin (M2M) app for this tenant.

Create a new terraform.tfvars for prod:

auth0_domain        = "prod-mycompany.us.auth0.com"
auth0_client_id     = "PROD_CLIENT_ID"
auth0_client_secret = "PROD_CLIENT_SECRET"


Reuse the same .tf files — they now serve as the blueprint for your production tenant.

Run:

terraform workspace new prod
terraform init
terraform apply


Terraform will now recreate everything in the new production tenant automatically.

🧭 10. Confirm Destination (Success Check)

You know you’ve reached your destination when:

terraform plan


Shows:

No changes. Your infrastructure matches the configuration.


That means:

✅ Terraform has imported all existing resources

✅ Your .tfstate matches your Auth0 environment

✅ You can now version-control your Auth0 config safely

💡 Pro Tips

Keep separate workspaces or state files for each tenant (dev, stage, prod).

Use terraform state list to view imported resources.

If something changes manually in Auth0, run terraform plan to detect drift.

Use terraform import only once per resource — after that, Terraform manages it.

🧹 Example Commands Summary
Command	Purpose
terraform init	Initialize provider & backend
terraform plan	Check what will change
terraform apply	Apply changes
terraform import	Import manually created resources
terraform state list	View resources in state
terraform workspace new prod	Create isolated environment for prod
terraform plan -var-file="prod.tfvars"	Plan against prod variables
✅ Final State

When you run:

terraform plan


and see:

No changes. Your infrastructure matches the configuration.


That’s your 🎯 “destination achieved” moment —
You’ve successfully migrated manual Auth0 resources under Terraform management and deployed your production tenant cleanly.
