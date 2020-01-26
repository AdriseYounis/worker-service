# Using Azure Container Registry with Kubernetes

- [x] Create a kubernetes deployment using an image which is stored in azure container registry 

## Create a dotnet core worker service 

```
dotnet new worker  
```

## Docker File

The `docker file` in this repository : 

- [x] Gets `aspnet:3.0` & `dotnet/core/sdk:3.0`
- [x] `Restores`, `builds` and `publishes` the `worker-service.csproj`
- [x] Places the `worker-service.csproj` at `app` directory and add an `entry point`

## Create a Service Principal For Azure Container Registry

- [x] Run the below command on `powershell` or `terminal` to login to `azure`

```
az login
```

- [x] Set `account subscription`

```
az account set --subscription <SUBSCRIPTION_ID>
```

- [x] Create `azure resource group`
```
az group create --location northeurope --name <RESOURCE_GROUP_NAME>
```

- [x] Create `azure container registry`

```
az acr create --name <REGISTRY_NAME> --resource-group <RESOURCE_GROUP_NAME> --sku Basic

```

- [x] Create a `service principal`

```
az ad sp create-for-rbac 

--scopes /subscriptions/<SUBSCRIPTION_ID>/resourcegroups/<RG_NAME>/providers/Microsoft.ContainerRegistry/registries/<REGISTRY_NAME> 

--role Contributor 

--name <SERVICE_PRINCIPAL_NAME>
```

- [x] Below `json object` will be displayed 

```
{
  "appId": "<appId>",
  "displayName": "<displayName>",
  "name": "<name>",
  "password": "<password>",
  "tenant": "<tenant>"
}
```
## Push Docker Image to Azure Container Registry 

- [x] Using `docker CLI` log into `ACR`

```
docker login <REGISTRY_NAME> -u <CLIENT_ID>
```

- [x] Tag Image 

```
docker tag <TAG_NAME> <ACR_NAME>.azurecr.io/<IMAGE_NAME>
```

- [x] Push Image 

```
docker push <ACR_NAME>.azurecr.io/<IMAGE_NAME>
```

## Configure Kubernetes to use Azure Container Registry Image

- [x] Create `Kubernete Secrets`

```
kubectl create secret docker-registry <SECRET_NAME> 

--docker-server <ACR_NAME>.azurecr.io 

--docker-email <EMAIL> 

--docker-username=<SP_APP_ID>

--docker-password <SP_PASSWORD>

```

- [x] View `secret` created 

```
kubectl get secret <SECRET_NAME>
```

## Deployment.yaml File 

- [x] Set `image property` to the below value

```
image: <ACR_NAME>.azurecr.io/<IMAGE_NAME>:<IMAGE_TAG>
```

- [x] `Include`the `secret created` above when `pulling the image`

```
imagePullSecrets:
    - name: dr-secrets
```

- [x] `Deploy` deployment.yaml file 

```
kubectl apply -f ./deployment.yaml 
```

- [x] A `POD` should be `created` with the `container image` pulled from `azure container registry`