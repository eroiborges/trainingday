# Azure Event Hub

## Criar um Event Hub

```
az eventhubs namespace create --resource-group $rgnameaz --name "ns-"$resourcename --location $location \
--sku Standard --enable-auto-inflate --maximum-throughput-units 2 

az eventhubs eventhub create --name "eh-"$resourcename --resource-group $rgnameaz \
--namespace-name "ns-"$resourcename --partition-count 2 --cleanup-policy Delete
```

## Criar Storage Account

```
export storagename="stor"$resourcename
export storcontainername="ehstatus"

az storage account create -n $storagename -g $rgnameaz -l $location --sku Standard_LRS --allow-shared-key-access true
az storage container create --name $storcontainername --account-name $storagename
export storageid=$(az storage account show --name $storagename -g $rgnameaz -o tsv --query id)
```

## Aplicaçao Sender e Receiver no Container instance

```
export instancename="appsender"
az container create \
    --resource-group $rgnameaz --name $instancename --location $location \
    --image acrbytraining.azurecr.io/evseender:v2 \
    --dns-name-label $resourcename"-sender" --ports 8080 --os-type Linux \
    --memory 1 --cpu 1 --restart-policy Always \
    --registry-login-server acrbytraining.azurecr.io \
    --registry-username deploy \
    --registry-password 0uoiJSpSs8RhOBDPCMm1m0SwRUbjIFtp93hhkdcqE8+ACRC93xl7 \
    --assign-identity \
    --environment-variables EVENT_HUB_FULLY_QUALIFIED_NAMESPACE="ns-"$resourcename.servicebus.windows.net \
    EVENT_HUB_NAME="eh-"$resourcename STORAGE_URL=storg2rvj68knc7d9.blob.core.windows.net STORAGE_CONTAINER=$storcontainername

export instancename2="appreceiver"
az container create \
    --resource-group $rgnameaz --name $instancename2 --location $location \
    --image acrbytraining.azurecr.io/evrcver:v5 \
    --dns-name-label $resourcename"-rcv" --ports 8080 --os-type Linux \
    --memory 1 --cpu 1 --restart-policy Always \
    --registry-login-server acrbytraining.azurecr.io \
    --registry-username deploy \
    --registry-password 0uoiJSpSs8RhOBDPCMm1m0SwRUbjIFtp93hhkdcqE8+ACRC93xl7 \
    --assign-identity \
    --environment-variables EVENT_HUB_FULLY_QUALIFIED_NAMESPACE="ns-"$resourcename.servicebus.windows.net \
    EVENT_HUB_NAME="eh-"$resourcename STORAGE_URL=$storagename.blob.core.windows.net STORAGE_CONTAINER=$storcontainername SLEEPTIME=180
```

```
export ehsid=$(az eventhubs eventhub show --name "eh-"$resourcename --resource-group $rgnameaz --namespace-name "ns-"$resourcename -o tsv --query id)
export acisenderid=$(az container show --resource-group $rgnameaz --name appsender -o tsv --query identity.principalId)
export acireceiverid=$(az container show --resource-group $rgnameaz --name appreceiver -o tsv --query identity.principalId)

az role assignment create --assignee $acisenderid --role "Azure Event Hubs Data Sender" --scope $ehsid
az role assignment create --assignee $acireceiverid --role "Azure Event Hubs Data Receiver" --scope $ehsid
az role assignment create --assignee $acireceiverid --role "Storage Blob Data Contributor" --scope $storageid
```

## Referencias do módulo

1. [terminology in Azure Event Hubs](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-features)

2. [Choose between Azure messaging services](https://learn.microsoft.com/en-us/azure/service-bus-messaging/compare-messaging-services)

3. [Schema Registry in Azure Event Hubs](https://learn.microsoft.com/en-us/azure/event-hubs/schema-registry-concepts)

4. [Availability and consistency in Event Hubs](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-availability-and-consistency?tabs=dotnet)

5. [Authorize access to Azure Event Hubs](https://learn.microsoft.com/en-us/azure/event-hubs/authorize-access-event-hubs)

6. [Network security for Azure Event Hubs](https://learn.microsoft.com/en-us/azure/event-hubs/network-security)