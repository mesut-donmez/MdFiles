# Deployment steps for Pointr Cloud API
In this article, we will explain how to deploy Pointr Cloud API. 

## Prerequisites

- Supported platforms:  `macOS`,  `Linux`, or  `Windows Subsystem for Linux (WSL)`
- Install [azure-cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) and [helm](https://helm.sh/docs/intro/install). Convenient options for some platforms:
	- macOS:  `brew install azure-cli helm`.
	- Ubuntu/Debian:
		 ```bash
	      curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
	      curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
- Install cli tools for AKS management via `az aks install-cli`.
- Continue from deployment steps.

Alternatively, you can use `Azure Cloud Shell` which already comes in with the necessary tools installed.

## Deployment Steps

- A valid `Pointr Cloud` instance must be available. You also need to have a Service Principal available with sufficient permissions.
- Login to your Azure account via `az login`.
- Update the values in `params.cfg`.
- Run `./script` in this directory, to deploy Pointr Cloud API.
- The script will print connection strings, pod api adresses, and azure front door address at the end.

## Deployment Parameters
```
"POINTR_RESOURCE_GROUP" -> The resource group that will contain all services.
"POINTR_RESOURCE_LOCATION" -> Specifies on which location all services will be installed.
"POINTR_CLUSTER_NAME" -> Azure Kubernates Cluster Service (AKS) name.
"POINTR_ACR_NAME" -> Azure Container Registry (ACR) name
"POINTR_FRONTDOOR_NAME" -> Azure Front Door Service (AFD) name
"POINTR_CRUD_OPTION" -> Parameter specifying what the script will do
"DOCKER_REGISTRY_SERVER" -> The docker registry server information to be given to access Pointr's Private DockerHub pods
"DOCKER_USERNAME_POINTR" -> Pointr's Private DockerHub username
"DOCKER_PASSWORD_POINTR" -> Pointr's Private DockerHub access token

"POINTR_CLOUD_API_NAME" -> Azure WebApp name
"POINTR_CONTENT_API_ZIP_URL" -> Content Api zip file url

"BEACONSEARCHAPI_IMAGE_NAME" -> Beacon Search Service docker image name
"BEACONSEARCHAPI_IMAGE_VERSION" -> Beacon Search Service docker image version
"BEACONSEARCHAPI_CACHE_BEACONUPDATEINTERVAL" -> Beacon Search Service beacon update interval environment variable (Default: 300 sec)
"BEACONSEARCHAPI_CACHE_LOGPERSISTINTERVAL" -> Beacon Search Service log persist interval environment variable (Default: 300 sec)

"IDENTITYAPI_IMAGE_NAME" -> Identity Service docker image name
"IDENTITYAPI_IMAGE_VERSION" -> Identity Service docker image version

"AZURE_CONNECTION_STRING" -> Azure CDN Connection String
"AZURE_SASTOKENEXPIREMIN" -> Azure CDN SAS Token

"AZUREAD_ENABLED" -> Azure Active Directory Enabled (Default: false)
"AZUREAD_TENANTID" -> Azure AD Tenant Id
"AZUREAD_CLIENTID" -> Azure AD Client Id
"AZUREAD_SCOPE" -> Azure AD Scope
"AZUREAD_SECRET" -> Azure AD Secret Key
"AZUREAD_REDIRECT" -> Azure AD Redirect Address

"EVENTHUB_SASPRIMARYKEY" -> EventHub SAS Primary Key
"EVENTHUB_SBNAME" -> 
"EVENTHUB_EHNAME" -> 

"POINTR_MSSQL_CONNECTION_STRING" -> Database connection string templete to be used in the project

```
** NOTE: If SQL Server password is to be changed, both the `Password` field in the templete string and the value of `SA_PASSWORD` in the `./Azure/sql-server.yml` file must be changed. 

# FAQ

## How to connect `kubectl` to an existing cluster?

Replace `{RESOURCE_GROUP}` and `{CLUSTER_NAME}` with the desired values and run the following command:
```
az aks get-credentials -g {RESOURCE_GROUP} -n {CLUSTER_NAME} --overwrite-existing
```
This will associate the current `kubectl` context with the given cluster, so that the subsequent commands are executed on this cluster.

## How to create or update all services?
You should change the `POINTR_CRUD_OPTION` parameter in the `params.cfg` file to `"ALL_SERVICES"`. This will create all backend services and print the access address.

## How to create or update specific service?
You should change the `POINTR_CRUD_OPTION` parameter in the `params.cfg` file to given parameter name. 
```
"ONLY_CONTENTAPI" -> Create or Update Content Api
"ONLY_BEACONSEARCHAPI" -> Create or Update Beacon Search Api
"ONLY_IDENTITYAPI" -> Create or Update Identity Api
```
This will create all backend services and print the access address.

## How to delete all services?
You should change the `POINTR_CRUD_OPTION` parameter in the `params.cfg` file to `"DEL_EVERYTHING"`. This will delete everything from Azure. This action can take a while. You can see that operation is completed in script but Azure will take some time to delete everything. 

For more information visit https://learn.microsoft.com/en-us/azure/aks/update-credentials#update-aks-cluster-with-service-principal-credentials.
