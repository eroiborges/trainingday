## Azure Web app

1. Criar um App service Plan e uma WebApp

    ```
    az appservice plan create -g $rgnameaz  --location $location -n appserplan01 \
    --is-linux --number-of-workers 1 --sku P0V3

    az webapp create --name "webapp-"$resourcename --resource-group $rgnameaz \
    --plan appserplan01 -r PYTHON:3.12 
    ```

2. Visualizar e criar uma variavel de ambiente .

    ```
    az webapp config show --name "webapp-"$resourcename --resource-group $rgnameaz -o json

    az webapp config set --name "webapp-"$resourcename --resource-group $rgnameaz --always-on false

    az webapp config appsettings set --name "webapp-"$resourcename --resource-group $rgnameaz \
    --settings APIVERSION=v1.2
    ```

3. Publicar uma aplicação

    ```
    az webapp deploy --resource-group $rgnameaz --name "webapp-"$resourcename --src-path ./app/python-quickstart/quick.zip
    ```

4. Criar um Slot de deployment com uma variavel de ambiente.

    ```
    az webapp deployment slot create --resource-group $rgnameaz --name "webapp-"$resourcename \
    --slot appv2
    
    az webapp config appsettings set --name "webapp-"$resourcename --resource-group $rgnameaz \
    --slot appv2 --settings APIVERSION=v2
    
    az webapp config appsettings list --name "webapp-"$resourcename --resource-group $rgnameaz \
    --slot appv2
    
    az webapp deploy --resource-group $rgnameaz --name "webapp-"$resourcename \
    --src-path ./app/python-quickstart/quick.zip --slot appv2
    ```

## Referencias do módulo

1. [Getting started with Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/getting-started?pivots=stack-net)

2. [Environment variables and app settings in Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/reference-app-settings?tabs=kudu%2Cdotnet)

3. [Deploy files to Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/deploy-zip?tabs=cli)

4. [Set up staging environments in Azure App Service](https://learn.microsoft.com/en-us/azure/app-service/deploy-staging-slots?tabs=portal)

5. [Deployment best practices](https://learn.microsoft.com/en-us/azure/app-service/deploy-best-practices)
