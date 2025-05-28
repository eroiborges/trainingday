## Azure Container Instance

1. Criar duas instancias do container instance (para uso no LAB de APIM)

    ```
    export instancename_prefix="app"

    for i in 01 02
    do
        instancename="${instancename_prefix}${i}"

        az container create \
            --resource-group $rgnameaz --name $instancename --location $location \
            --image acrbytraining.azurecr.io/getheaderspython:v1 \
            --dns-name-label "${resourcename}${i}" --ports 8080 --os-type Linux \
            --memory 1 --cpu 1 \
            --registry-login-server acrbytraining.azurecr.io \
            --registry-username deploy \
            --registry-password 0uoiJSpSs8RhOBDPCMm1m0SwRUbjIFtp93hhkdcqE8+ACRC93xl7
    done
    ```
## Referencias do módulo

1. [Deploy a container instance](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-quickstart)

2. [Container groups in Azure Container Instances](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-container-groups)

3. [Virtual network scenarios and resources](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-virtual-network-concepts)

4. [Mount an Azure file share in Azure Container Instances](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-volume-azure-files)

5. [Update containers in Azure Container Instances](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-update)

6. [Monitor Azure Container Instance](https://learn.microsoft.com/en-us/azure/container-instances/monitor-azure-container-instances)