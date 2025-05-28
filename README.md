# Modernização de aplicações com serviços gerenciados no Microsoft Azure

**Objetivo:** Este workshop prático foi projetado para equipá-lo com as conhecimento necessáro para projetar soluções em Cloud Computing utilizando a plataforma Microsoft Azure para os projetos de Modernizaçao de aplicações e Inteligência Artificial. 

**Audiência:**  Arquitetoa de Solução, Desenvolvedores e Especialista em Cloud.

**Tecnologias:**  Azure Containers services, Azure APIM e App Services.

**Requisitos:**

1. Computador individual para a execução de laboratórios com acesso à internet.
2. Uma Subscription Azure.

**Documentação de referência**

Todo o contexto do treinamento é baseado na documentação do Microsoft Azure, na comunidade técnica ou na página pública do GitHub que pode ser usada para aprofundar cada conteúdo fora das atividades do treinamento.

## Links uteis

* [Cloud Adoption Framework](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/)
* [Terraform CAF EnterpriseScale](https://github.com/Azure/terraform-azurerm-caf-enterprise-scale)
* [Azure Verified Modules](https://azure.github.io/Azure-Verified-Modules/)


## instruções iniciais

O Laboratorio foi desenvolvimento para executar através do AzureCLI com shell bash. Você pode utilizar uma instalação local em seu computador com o [WSL2](https://learn.microsoft.com/en-us/windows/wsl/install) ou através do [Azure Cloud Shell](<https://learn.microsoft.com/pt-br/azure/cloud-shell/overview>)

Versões do AZCli utilizadas:

| Ambiente | versão |
| -------- | ------ |
| **Desktop** | Azure-cli:2.62.0 |
| **CloudShell** | azure-cli: 2.65.0|
| | |

## Modulos

1. [Desenvolver a Arquitetura](Architecture.md)
2. [Resource Providers](rp.md)
3. [Setup base](setup.md)
4. [Azure App Services](appservice.md)
5. [Azure Container Instance](containerinstance.md)
6. [Azure Event Hub](eventhub.md)
7. [Azure API Managament](apim.md)
8. [Azure Kubernetes Services](kubernetes.md)
9. [Azure Containers Apps](containerapps.md)
