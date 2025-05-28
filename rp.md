## Registrar Resource Provider

  ```
  export networkprovider=$(az provider show --namespace Microsoft.Network -o tsv --query registrationState)

    if [ "$networkprovider" != "Registered" ]; then
        az provider register --namespace Microsoft.Network
        echo "Provider is not registered. Running the command..."
    fi

  export ksprovider=$(az provider show --namespace Microsoft.KeyVault -o tsv --query registrationState)

    if [ "$ksprovider" != "Registered" ]; then
        az provider register --namespace Microsoft.KeyVault
        echo "Provider is not registered. Running the command..."
    fi

  export Insightsprovider=$(az provider show --namespace Microsoft.Insights -o tsv --query registrationState)

    if [ "$Insightsprovider" != "Registered" ]; then
        az provider register --namespace Microsoft.Insights
        echo "Provider is not registered. Running the command..."
    fi

  export acaprov=$(az provider show --namespace Microsoft.App -o tsv --query registrationState)
  export acainsight=$(az provider show --namespace Microsoft.App -o tsv --query registrationState)
  export aciinsight=$(az provider show --namespace Microsoft.ContainerInstance -o tsv --query registrationState)

    if [ "$acaprov" != "Registered" ]; then
        az provider register --namespace Microsoft.App
        echo "Provider is not registered. Running the command..."
    fi

    if [ "$acainsight" != "Registered" ]; then
        az provider register --namespace Microsoft.OperationalInsights
        echo "Provider is not registered. Running the command..."
    fi

    if [ "$aciinsight" != "Registered" ]; then
        az provider register --namespace Microsoft.OperationalInsights
        echo "Provider is not registered. Running the command..."
    fi

  export appservice=$(az provider show --namespace Microsoft.Compute -o tsv --query registrationState)

    if [ "$acaprov" != "Registered" ]; then
        az provider register --namespace Microsoft.Compute
        echo "Provider is not registered. Running the command..."
    fi

  export appservice=$(az provider show --namespace Microsoft.Web -o tsv --query registrationState)

    if [ "$acaprov" != "Registered" ]; then
        az provider register --namespace Microsoft.Web
        echo "Provider is not registered. Running the command..."
    fi

  export appservice=$(az provider show --namespace Microsoft.EventHub -o tsv --query registrationState)

    if [ "$acaprov" != "Registered" ]; then
        az provider register --namespace Microsoft.EventHub
        echo "Provider is not registered. Running the command..."
    fi

  ```