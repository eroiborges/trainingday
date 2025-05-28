## Variaveis de ambiente

Definir as seguintes variaveis de ambiente. Caso a subscrição em uso tenha restrições de implementação em alguma região do Azure, alterar o valor da varivel location para uma região disponivel. Para obter o nome das regioes, utilizar o comando ``` az account list-locations --query '[].name' ```

  ```
  export resourcename="rs"$(tr -dc a-z0-9 </dev/urandom | head -c 11; echo)
  export rgnameaz="rg-$resourcename"
  export location="centralus"
  export vmsize="standard_b4ms" #"Standard_B2ms" #"Standard_D2ads_v5" #"Standard_D2s_v5"
  export az_vnetname="vnet-"$resourcename
  ```

# Criar Resource Group

Criar o Resource Groups para os deployments "Cloud"

```
az group create --name $rgnameaz --location $location
```


## Virtual Network

Criar a VNET que representa o ambiente Azure.

  ```
  az network nsg create --resource-group $rgnameaz --name nsg-azhosts --location $location

  az network vnet create --resource-group $rgnameaz --name $az_vnetname --address-prefixes 10.160.0.0/20 --subnet-name azhosts --subnet-prefixes 10.160.0.0/27 --location $location --network-security-group nsg-azhosts 

  az network vnet subnet create --name appservice --resource-group $rgnameaz --vnet-name $az_vnetname --address-prefix 10.160.0.64/26 --delegations Microsoft.Web/serverFarms

  az network vnet subnet create --name acasubnet --resource-group $rgnameaz --vnet-name $az_vnetname --address-prefix 10.160.4.0/24 --delegations Microsoft.App/environments
  ```

## Log Analytic Workspace

Criar um workspace do Log Analytics para hospedagem de logs de diagnostico.

  ```
  az monitor log-analytics workspace create --name $resourcename --resource-group $rgnameaz --location $location --sku PerGB2018
  ```
