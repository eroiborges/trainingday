# Azure Container APPs (ACA)

## Provisionamento ACA

1. Vamos criar uma instancia simples e aprender como o ACA funciona. Neste momento, nao vamos integrar com nosso ambiente, apenas provisionar um recurso.

    ```
    az extension add --name containerapp --upgrade --allow-preview true

    export lawid=$(az monitor log-analytics workspace show --name $resourcename --resource-group $rgnameaz --query customerId -o tsv)

    export lawkey=$(az monitor log-analytics workspace get-shared-keys --name $resourcename --resource-group $rgnameaz -o tsv --query primarySharedKey)

    az containerapp up \
      --name "apps01-"$resourcename \
      --resource-group $rgnameaz \
      --location $location \
      --environment "env01-"$resourcename \
      --image mcr.microsoft.com/k8se/quickstart:latest \
      --target-port 80 \
      --ingress external \
      --logs-workspace-id $lawid \
      --query properties.configuration.ingress.fqdn
    ```
  
    ```
    az containerapp revision list --name "apps01-"$resourcename --resource-group $rgnameaz -o table
    ```

    ```  
    az containerapp update --name "apps01-"$resourcename --resource-group $rgnameaz --image mcr.microsoft.com/azuredocs/aci-helloworld:latest

    ou

    az containerapp revision copy --name "apps01-"$resourcename --resource-group $rgnameaz --revision-suffix apps02 --cpu 0.5 --memory 1Gi
    ```

    ```
      az containerapp up \
        --name "apps02-"$resourcename \
        --resource-group $rgnameaz \
        --location $location \
        --environment "env01-"$resourcename \
        --image mcr.microsoft.com/k8se/quickstart:latest \
        --target-port 80 \
        --ingress external \
        --logs-workspace-id $lawid \
        --query properties.configuration.ingress.fqdn
    ```

    ```
      az containerapp revision set-mode --mode multiple --name "apps02-"$resourcename --resource-group $rgnameaz

      az containerapp update --name "apps02-"$resourcename --resource-group $rgnameaz --image mcr.microsoft.com/azuredocs/aci-helloworld:latest
    ```

    ```      
      az containerapp create \
        --name "apps03-"$resourcename \
        --resource-group $rgnameaz \
        --environment "env01-"$resourcename \
        --image acrbytraining.azurecr.io/evrcver:v5 --registry-server acrbytraining.azurecr.io --registry-username deploy --registry-password 0uoiJSpSs8RhOBDPCMm1m0SwRUbjIFtp93hhkdcqE8+ACRC93xl7 \
        --env-vars EVENT_HUB_FULLY_QUALIFIED_NAMESPACE="ns-"$resourcename.servicebus.windows.net EVENT_HUB_NAME="eh-"$resourcename STORAGE_URL=$storagename.blob.core.windows.net STORAGE_CONTAINER=$storcontainername SLEEPTIME=5 \
        --min-replicas 1 --max-replicas 10 \
        --scale-rule-name eventhub-rule \
        --scale-rule-type azure-eventhub \
        --scale-rule-metadata "topic=eh-rs8zvtarekd2d" \
                                "consumerGroup=$Default" \
                                "unprocessedEventThreshold=10" \
                                "blobContainer=ehstatus" \
                                "storageAccount=storrs8zvtarekd2d" \
        --scale-rule-auth "connection=azure-identity" "storageConnection=azure-identity" \
        --system-assigned
    ```  

    ```
      export ehsid=$(az eventhubs eventhub show --name "eh-"$resourcename --resource-group $rgnameaz --namespace-name "ns-"$resourcename -o tsv --query id)
      export acaapp03receiverid=$(az containerapp show -n "apps03-"$resourcename --resource-group $rgnameaz -o tsv --query identity.principalId)
      export storageid=$(az storage account show --name $storagename -g $rgnameaz -o tsv --query id)

      az role assignment create --assignee $acaapp03receiverid --role "Azure Event Hubs Data Receiver" --scope $ehsid
      az role assignment create --assignee $acaapp03receiverid --role "Storage Blob Data Contributor" --scope $storageid
    ```

## Provisionamento ACA Corporativo - "Para chamar de meu"

1. Criar uma Subnet para o ACA

    ```
    az network vnet subnet create --name acasubnet --resource-group $rgnameaz --vnet-name $az_vnetname --address-prefix 10.160.4.0/24 

    az network vnet subnet update --name acasubnet --resource-group $rgnameaz --vnet-name $az_vnetname --delegations Microsoft.App/environments

    export acasubnetid=$(az network vnet subnet list --resource-group $rgnameaz --vnet-name $az_vnetname -o tsv --query "[?name=='acasubnet'].id")
    ```

2. Vamos provisionar um **Environment** dedicado

    ```
    az containerapp env create --name $resourcename"-env2" --resource-group $rgnameaz \
    --location "$location" --infrastructure-subnet-resource-id $acasubnetid \
    --logs-workspace-id $lawid --logs-workspace-key $lawkey --logs-destination log-analytics --enable-workload-profiles true --internal-only true

    export ENVIRONMENT_DEFAULT_DOMAIN=$(az containerapp env show --name $resourcename"-env" --resource-group $rgnameaz --query properties.defaultDomain --out tsv)

    export ENVIRONMENT_STATIC_IP=$(az containerapp env show --name $resourcename"-env" --resource-group $rgnameaz --query properties.staticIp --out tsv)

    export VNET_ID=$(az network vnet show --resource-group $rgnameaz --name $az_vnetname -o tsv --query id)
    ```

3. Criar Zona Privada

    ```
    az network private-dns zone create \
      --resource-group $rgnameaz \
      --name $ENVIRONMENT_DEFAULT_DOMAIN

    az network private-dns link vnet create \
      --resource-group $rgnameaz \
      --name az_vnetname \
      --virtual-network $VNET_ID \
      --zone-name $ENVIRONMENT_DEFAULT_DOMAIN -e true

    az network private-dns record-set a add-record \
      --resource-group $rgnameaz \
      --record-set-name "*" \
      --ipv4-address $ENVIRONMENT_STATIC_IP \
      --zone-name $ENVIRONMENT_DEFAULT_DOMAIN
    ```

+ Criar um Forward condicional no DNS do servidor Windows para a zona da variavel $ENVIRONMENT_DEFAULT_DOMAIN e enviar para o IP de Inbound do Private Resolver.

> Como eu testo um registro **\"*"** com o NSLOKKUP?

4. Criar Workload Profile

    ```
    az containerapp env workload-profile add --name $resourcename"-env" --resource-group $rgnameaz --workload-profile-name dedicado --max-nodes 2 --min-nodes 1 --workload-profile-type D4
    ```

5. Criar dos apps, um com o ingress interno e o outro externo. Qual a diferença?

    ```
    az containerapp create \
      --resource-group $rgnameaz \
      --name $resourcename"-apps02" \
      --target-port 80 \
      --ingress internal \
      --image mcr.microsoft.com/k8se/quickstart:latest \
      --environment $resourcename"-env" \
      --workload-profile-name "dedicado" \
      --query properties.configuration.ingress.fqdn


    az containerapp create \
      --resource-group $rgnameaz \
      --name $resourcename"-apps03" \
      --target-port 80 \
      --ingress external \
      --image mcr.microsoft.com/k8se/quickstart:latest \
      --environment $resourcename"-env" \
      --workload-profile-name "dedicado" \
      --query properties.configuration.ingress.fqdn
    ```

## Referencias do módulo

1. [Workload profiles](https://learn.microsoft.com/en-us/azure/container-apps/workload-profiles-overview)

2. [virtual network to an Azure Container Apps environment](https://learn.microsoft.com/en-us/azure/container-apps/vnet-custom?tabs=bash&pivots=azure-cli)

3. [Ingress in Azure Container Apps](https://learn.microsoft.com/en-us/azure/container-apps/ingress-overview)