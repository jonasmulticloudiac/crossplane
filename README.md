# Install argocd
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


# Service Type LoadBalancer
 kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
 kubectl get svc -n argocd

## or Port-Forward
 k port-forward svc/argocd-server  8080:80 -n argocd

# login Using the cli 

ARGOCD_PASSWD=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)

user: admin 
password : <get with kubectl>







# cli argocd
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64 

chmod +x /usr/local/bin/argocd

 argocd login localhost:8080 --username admin --password  $ARGOCD_PASSWD  --insecure


===================================================================================================
# Configure Azure provider
# az ad sp create-for-rbac --sdk-auth --role Owner --scopes="/subscriptions/7c2c99b3-6787-4508-8125-7b2dd7a61aac" -n "crossplane-sp-rbac" > "creds.json"

# if which jq > /dev/null 2>&1; then
  AZURE_CLIENT_ID=$(jq -r ".clientId" < "./creds.json")
else
  AZURE_CLIENT_ID=$(cat creds.json | grep clientId | cut -c 16-51)
fi

# RW_ALL_APPS=1cda74f2-2616-4834-b122-5cb1b07f8a59
# RW_DIR_DATA=78c8a3c8-a07e-4b9e-af1b-b5ccab50a175
# AAD_GRAPH_API=00000002-0000-0000-c000-000000000000

# az ad app permission add --id "${AZURE_CLIENT_ID}" --api ${AAD_GRAPH_API} --api-permissions ${RW_ALL_APPS}=Role ${RW_DIR_DATA}=Role
# az ad app permission grant --id "${AZURE_CLIENT_ID}" --api ${AAD_GRAPH_API} --expires never > /dev/null
# az ad app permission admin-consent --id "${AZURE_CLIENT_ID}"



# To Create a secret use, 
$ kubectl create secret generic azure-creds -n crossplane --from-file=creds=./creds.json

Name of the file is creds.json and namespace is crossplane, change it if you want. 

About Crossplane: https://foxutech.com/crossplane-let-your-kubernetes-to-build-your-cloud-infrastructure/[https://foxutech.com/crossplane-let-your-kubernetes-to-build-your-cloud-infrastructure/]
