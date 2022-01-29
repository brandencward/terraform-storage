## Terraformy

This is a basic repo for illustrating the structure of a simple terraform project

Command line to generate a Terrafomr Plan:

Cd into the repo directory

```
terraform plan -var-file env/sandbox.tfvars -out sandbox.tfplan
terraform apply sandbox.tfplan
```