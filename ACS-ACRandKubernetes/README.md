# ACS, ACR and Kubernetes workshop
A small workshop to setup Kubernetes orchestration using Azure Container Service, integrated with the (currently in preview) Azure Container Registry.


## Create Resource Group
To start the construction of the Container Service, Registry and Kubernetes resources, it is best to start by creating a Resource Group.  This has two benefits

1. It allows you to find and list the resources quickly
2. You can tear down the workshop resources once you've finished to stop burning through credits


```
az group create --name=kubeResourceGroup -l=westeurope
```

```javascript
{
  "id": "/subscriptions/63bb1026-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/kubeResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "kubeResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## Create the Azure Container Registry in the UI

Rework this for the CLI <- Not working in GA release!


## Create the service principal
The Service Principal (SP) is used for authentication purposes between ACS and ACR, and allows a more secure way of being able to manage the integration of the services than an admin username and password.

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/63bb1026-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
```

```javascript
{
  "appId": "a9c3becf-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "displayName": "azure-cli-2017-03-21-09-39-10",
  "name": "http://azure-cli-2017-03-21-09-39-10",
  "password": "ad33eefc-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenant": "72f988bf-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

### Test login
It's a good idea to test that the SP is working, and allows you to authenticate.  You can do this via the az cli, but do bear in mind you will need to re-authentricate back into your Azure account after to move on with your workshop.

```
az login --service-principal -u "a9c3becf-XXXX-XXXX-XXXX-XXXXXXXXXXXX" -p "ad33eefc-XXXX-XXXX-XXXX-XXXXXXXXXXXX" --tenant "72f988bf--XXXX-XXXX-XXXX-XXXXXXXXXXXX"
```


## Assign Service Principal to ACR
Once the SP has been created, assign it to the ACS you created earlier.

```
az role assignment create --scope /subscriptions/63bb1026-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcegroups/kubeResourceGroup/providers/Microsoft.ContainerRegistry/registries/kubeacs --role Owner --assignee "a9c3becf-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
```


## Create the cluster and assign SP

```
az acs create -n kube -d ms-kube -g kubeResourceGroup --generate-ssh-keys --orchestrator-type kubernetes --service-principal "a9c3becf-XXXX-XXXX-XXXX-XXXXXXXXXXXX" --client-secret "ad33eefc-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
```

```javascript
{
  "id": "/subscriptions/63bb1026-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/kubeResourceGroup/providers/Microsoft.Resources/deployments/azurecli1490089441.6333960",
  "name": "azurecli1490089441.6333960",
  "properties": {
    "correlationId": "448b7fb1-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westeurope"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-03-21T09:54:25.708282+00:00"
  },
  "resourceGroup": "kubeResourceGroup"
}
```

## Install kubectl
```
sudo az acs kubernetes install-cli
```


## Get credentials
The Kubernetes cli needs the location and credentials for the newly created cluster.  This is stored on the ~/.kube directory

```
az acs kubernetes get-credentials --resource-group=kubeResourceGroup --name=kube
```


## Docker Login
To be able to push a constructed Docker image to your new Container Registry, the docker client will need to authenticate.

```
docker login -u  "a9c3becf-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  -p "ad33eefc-XXXX-XXXX-XXXX-XXXXXXXXXXXX" kubeacs.azurecr.io
```

## Build Docker image 
Use the Dockerfile within this GitHub repository, along with the index.html.  Create a clean directory, and drop these files in there.  Build the image, ready for sending to the ACR.

### Build image
(note the full stop!)
```
docker build -t hello-london .
```

### Tag the image to the registry
```
docker tag hello-london kubeacs.azurecr.io/demos/hello-london
```

### Push the image to the registry
For Kubernetes to run your container, the image will need to be pushed for storage to the Container Service

```
docker push kubeacs.azurecr.io/demos/hello-london
```

## Setup the kube secrets for deployments
For Kubernetes to be able to authenticate and access images in your Container Registry, a secrets file (containing authorisation details in encrypted form) needs to be generated.   This secrets file is used when you create the pod, and allows all Kubernetes nodes to pull the image and instantiate a comntainer.

```
kubectl create secret docker-registry msregistrykey --docker-server=kubeacs.azurecr.io --docker-username="a9c3becf-XXXX-XXXX-XXXX-XXXXXXXXXXXX" --docker-password="ad33eefc-XXXX-XXXX-XXXX-XXXXXXXXXXXX" --docker-email=XXXX@microsoft.com
```


## Build Pod
All the elements are now in place to spin up the Kubernetes pod.  within the nginx.yml file we have defined the container, the router (for traffic) which utilised Azure Load balnacers to publish the application.

```
kubectl create -f ./nginx.yml
```

## Show the UI
Your pod should now be running (after a few seconds), you can either use some of the kubectl commands later in the document, or spin up a tunnel from your machine to the Kubernetes administration console.  
```
az acs kubernetes browse -n kube -g kubeResourceGroup
```

## Some other things to try
How about some other things to try:

- Push load onto the published service and see how the resource utilisation holds up
- Scale the number of replicas up or down to see performance increase/decrease
- Tear down and replace your container

## Some useful kubectl commands

### Show Pods
```
kubectl get pods
```

### Show Pod history
```
kubectl describe pods nginx-demo-360701834-h57ht
```

### Show deployments
```
kubectl get deployments --all-namespaces
```

### Delete a deployment
```
kubectl delete deployment nginx-demo-r
```

### Get services
```
kubectl get service
```


