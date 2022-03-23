## terraform-storage

This is a basic repo for illustrating the structure of a simple terraform storrage account. It utilizes terraform-aks and an application to test.


General Setup and Testing:

you will need a generated public key for one of these steps

cat .ssh/id_rsa.pub

in the terminal

az login

az account set --subscription SUBSCRIPTIONID

Clone the terraform-storage Repo located here: https://github.com/brandencward/terraform-storage

cd into terraform-storage

terraform init

terraform plan -var-file env\base.tfvars -out storage.tfplan

terraform apply storage.tfplan

Clone the terraform-aks Repo located here: https://github.com/brandencward/terraform-aks

cd into terraform-aks

terraform init

terraform plan -var-file env\sandbox.tfvars -out sandbox.tfplan

terraform apply sandbox.tfplan

az aks get-credentials --resource-group sandbox-resources --name sandbox-aks1

Install helm and drop in local path

Install nginx ingress controller with this command utilizing helm:
helm upgrade --install ingress-nginx ingress-nginx  --repo https://kubernetes.github.io/ingress-nginx  --namespace ingress-nginx --create-namespace

git clone https://github.com/mbmcmullen27/blinky-helm.git

Open and edit:
\blinky-helm\values.yaml
change 

```diff
--  tag: "arm"
++  tag: "amd64"
```

\blinky-helm\templates\ingress.yaml
change line 41 to a -
```diff
--   - host: {{ .host | quote }}
++   -
```

Install via helm:
helm install blinky .

kubectl get services -A
note the EXTERNAL-IP for the  ingress-nginx-controller

Use Blinky to see if your app is running:
curl http://IP/app/blinky

Destroy so you don't get charged:

cd terraform-aks\
terraform destroy -var-file env\sandbox.tfvars
enter public key used in the beginning
should be 3 things being destroyed
Type Yes

cd ..\terraform-storage\
terraform destroy -var-file env\base.tfvars
terraform destroy -var-file env\base.tfvar
should be 3 things being destroyed
Type Yes

kubectl get pods
should show a message saying it cannot connect

In Azure Make sure nothing is listed under Resources for your subscription
If the network thing is there delete it
