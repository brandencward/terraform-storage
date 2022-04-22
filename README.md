## terraform-aks

This is a basic repo for illustrating the structure of a simple terraform project. Utilizes the resource created by [terraform-aks](https://github.com/brandencward/terraform-aks) as the frontend. It uses helm as the package installer.

General Setup and Testing:

you will need a generated public key for one of these steps

If you already have a key you can cat it to a terminal for easy reference

```console
cat .ssh/id_rsa.pub
```

**Login to Azure / Set Subscription**

```console
az login

az account set --subscription SUBSCRIPTIONID
```

**Setup terraform-storage backend**

```console
git clone https://github.com/brandencward/terraform-storage.git

cd terraform-storage

terraform init

terraform plan -var-file env\base.tfvars -out storage.tfplan

terraform apply storage.tfplan
```
**Setup terraform-aks**

```console
git clone https://github.com/brandencward/terraform-aks.git

cd terraform-aks

terraform init

terraform plan -var-file env\sandbox.tfvars -out sandbox.tfplan

terraform apply sandbox.tfplan

az aks get-credentials --resource-group sandbox-resources --name sandbox-aks1
```

*Must have [helm](https://helm.sh/docs/intro/install/) installed and setup in local path for this step*

**Setup nginx ingress controller**

```console
helm upgrade --install ingress-nginx ingress-nginx  --repo https://kubernetes.github.io/ingress-nginx  --namespace ingress-nginx --create-namespace
```
*This section covers deploying a fun little application called blinky in order to test. Any application can be used in its place though. I just thought this was a fun one. :neutral_face:*

**Test App**

```console
git clone https://github.com/mbmcmullen27/blinky-helm.git
```

Open and edit:
\blinky-helm\values.yaml

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

**Install Blinky via Helm**
```console
helm install blinky .

kubectl get services -A
```
*note the EXTERNAL-IP for the  ingress-nginx-controller*


**Test with Blinky**
```console
curl http://**IP**/app/blinky
```

**Destroy so you don't get charged:exclamation:**

```console
cd terraform-aks
terraform destroy -var-file env\sandbox.tfvars
```
enter public key used in the beginning
should be 3 things being destroyed
Type Yes

```console
cd terraform-storage
terraform destroy -var-file env\base.tfvars
terraform destroy -var-file env\base.tfvar
```
should be 3 things being destroyed
Type Yes

```console
kubectl get pods
```
should show a message saying it cannot connect

In Azure Make sure nothing is listed under Resources for your subscription
If the network thing is there delete it
