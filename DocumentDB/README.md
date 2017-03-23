## Create Resource Group
```
az group create --name=ddbResourceGroup -l=westeurope
```

## Create DocumentDB Mongo instance

```
az documentdb create -n mongodemo-ms -g ddbResourceGroup --kind MongoDB

{
  "consistencyPolicy": {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://mongodemo-ms.documents.azure.com:443/",
  "failoverPolicies": [
    {
      "failoverPriority": 0,
      "id": "mongodemo-ms-westeurope",
      "locationName": "West Europe"
    }
  ],
  "id": "/subscriptions/63bb1026-XXXX-XXXX-XXXX-XXXXXXXXXX/resourceGroups/ddbResourceGroup/providers/Microsoft.DocumentDB/databaseAccounts/mongodemo-ms",
  "ipRangeFilter": "",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "mongodemo-ms",
  "provisioningState": "Succeeded",
  "readLocations": [
    {
      "documentEndpoint": "https://mongodemo-ms-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "mongodemo-ms-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "ddbResourceGroup",
  "tags": {},
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://mongodemo-ms-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "mongodemo-ms-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
}
```

## Login to the portal and get the connection string
IMAGE

## Edit the app.js file
Change the connection string to allow the application to connect to the DocumentDB instance
```
 var url = 'mongodb://mongodemo-ms:XXXXXXXXXXXXXXX@mongodemo-ms.documents.azure.com:10250/?ssl=true';

```

## Run node connection to query DB via Mongo
This Node.js application will connect to the DocumentDB instance, insert a few records, search for them back, and then remove them.  

```
node app.js
```