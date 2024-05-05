### ExternalDNS

ExternalDNS synchronizes Kubernetes resources with external DNS records hosted on providers like:
AWS Route 53, Google Cloud DNS, AzureDNS, CloudFlare DNS etc. With this, we don’t need to manually create DNS entries every time we deploy a new application to our cluster. Cool, right?

```sh
### 1. Installing external DNS services
aws sts get-caller-identity --query Account --output text
aws route53 list-hosted-zones-by-name --output json --dns-name "solvweb.net." | jq -r '.HostedZones[0].Id'

### 2. Install cert-manager
### cert-manager is a CRD (Custom Resource Definition) that dynamically generates TLS/SSL certificates for our applications using Let’s Encrypt (although it also supports other issuers).

helm repo add bitnami https://charts.bitnami.com/bitnami
helm install --create-namespace -n cert-manager cert-manager bitnami/cert-manager \
  --create-namespace \
  --namespace cert-manager \
  --set installCRDs=true

### 3. Install Let's Encrypt 
### Note: Replace <my_email> with your email address. If you’re using an Ingress other than Nginx, you need to change the manifests above by setting the appropriate class.
### Run the command below to create them in the cluster.
kubectl apply -f cert-manager-staging.yaml -f cert-manager-prod.yaml 

