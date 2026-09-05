# Terraform Multi Env with Terraform Workspace

### Overview
There would be times when you need to manage multiple environments (dev, uat, prod etc.) from the same terraform code repository. Instead of using a git branching strategy to manage the different environments (1 tfstate file per branch), you can also use terraform workspaces.

### What is terraform workspaces?
Workspaces in Terraform are simply independently managed state files. A workspace contains everything that Terraform needs to manage a given collection of infrastructure, and separate Workspaces function like completely separate working directories. We can manage multiple environments with Workspaces.

Deletion/ destruction of one workspace will not impact the resources on another.

Reference: https://medium.com/devops-mojo/terraform-workspaces-overview-what-is-terraform-workspace-introduction-getting-started-519848392724#:~:text=TL%3BDR%3A,manage%20multiple%20environments%20with%20Workspaces. 
![image](https://github.com/luqmannnn/terraform-multi-env/assets/9068525/57fce044-145d-450f-bfcf-ddb6d91dde7e)

### What will we need to create terraform workspace?
1. Install terraform

### High level concepts
1. variable.tf = Stores the variables that we will use to inject into our main.tf files. These variables are all empty, and we will fetch the values from tfvars files.
2. tfvars files = Stores the exact values based on the environment that we are targeting.
3. main.tf = Stores the provider block and the resource block for S3 creation.

### Steps
1. After cloning this repository, make the necessary amendments to the dev.tfvars, uat.tfvars and prod.tfvars files with your own variable values; Replace the existing values.
2. Create the different terraform workspaces:
- Run `terraform init` followed by `terraform workspace new dev` to create a dev workspace, `terraform workspace new uat` to create a uat workspace, `terraform workspace new prod` to create a prod workspace.
- Run `terraform workspace list` to view all the workspaces you have.
<img width="817" alt="Screenshot 2023-09-18 at 8 48 06 PM" src="https://github.com/luqmannnn/terraform-multi-env/assets/9068525/a5b39dc5-133b-4cbd-9baf-174116eea071">
The "*" indicates the current workspace you are in.

- Run `terraform workspace select dev` to switch to the dev environment.

3. Inject the values from the terraform tfvars file into the variable.tf file to be used by the main.tf file.
- To do a plan for Dev environment, be sure to switch to the correct workspace, followed by `terraform plan -var-file=dev.tfvars`. This will inject the values from the dev.tfvars file into main.tf and the output of the S3 bucket name will correspond to the variables from dev.tfvars.
<img width="1052" alt="Screenshot 2023-09-18 at 8 55 27 PM" src="https://github.com/luqmannnn/terraform-multi-env/assets/9068525/564a3faa-2d92-4789-9a5c-d9d26cb038e9">

- The same steps can be done for your uat and prod environments as well.
<img width="1048" alt="Screenshot 2023-09-18 at 8 56 11 PM" src="https://github.com/luqmannnn/terraform-multi-env/assets/9068525/8d4bbe40-a1a8-4d25-b0a6-784b6cc612b1">

### Where are my terraform state files stored?
Terraform will store your state files in terraform.tfstate.d folder, separated by each environment (dev, uat, prod). Each workspace has its own tfstate file.

<img width="311" alt="Screenshot 2023-09-18 at 9 01 50 PM" src="https://github.com/luqmannnn/terraform-multi-env/assets/9068525/ed3f90b4-01cb-4cfe-b0ad-711fa3efd645">

### Further steps
If you would like to use Github Actions to run these pipelines, you may want to consider the below:
1. Remove any credentials from your local machine (AWS Secrets/ Access Keys) and upload them in Github Actions Secrets
2. Add the backend.tf file to store your state files on S3.
3. Add your .github/workflow/main.yml file that contains your Github Actions CI/CD steps which should include configuring AWS credentials, terraform init, terraform select workspace <env>, terraform plan -var-file=<env>.tfvars

