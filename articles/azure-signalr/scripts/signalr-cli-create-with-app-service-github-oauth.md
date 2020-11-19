---
title: Een web-app maken met behulp van de SignalR Service en GitHub-verificatie
description: Azure CLI-voorbeeldscript - Een web-app maken die gebruikmaakt van de service SignalR en GitHub-verificatie
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/22/2018
ms.author: zhshang
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ee9f0253f2ec27b4afbbb6c5dc1ff3f5c129fe8a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841798"
---
# <a name="create-a-web-app-that-uses-signalr-service-and-github-authentication"></a>Een web-app maken die gebruikmaakt van de service SignalR en GitHub-verificatie

Met dit voorbeeldscript maakt u een nieuwe resource voor de Azure SignalR-service, die vervolgens wordt gebruikt om realtime updates van inhoud te pushen naar clients. Met dit script worden ook een nieuwe web-app en een App Service-plan toegevoegd voor het hosten van een web-app van ASP.NET Core die de SignalR-service gebruikt. De web-app is geconfigureerd met app-instellingen voor het maken van verbinding met de nieuwe SignalR-serviceresource en voor het verifiëren met [GitHub-verificatie](https://developer.github.com/v3/guides/basics-of-authentication/). De web-app wordt ook geconfigureerd voor het gebruik van een implementatiebron van een lokale Git-opslagplaats.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Voorbeeldscript

Dit script maakt gebruik van de extensie *signalr* voor Azure CLI. Voer de volgende opdracht uit om de extensie *signalr* voor Azure CLI te installeren voordat u dit voorbeeldscript gaat gebruiken:

```azurecli-interactive
#!/bin/bash

#========================================================================
#=== Update these values based on your desired deployment username    ===
#=== and password.                                                    ===
#========================================================================
deploymentUser=<Replace with your desired username>
deploymentUserPassword=<Replace with your desired password>

#========================================================================
#=== Update these values based on your GitHub OAuth App registration. ===
#========================================================================
GitHubClientId=<Replace with your GitHub OAuth app Client ID>
GitHubClientSecret=<Replace with your GitHub OAuth app Client Secret>


# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate unique names for the SignalR service, resource group, 
# app service, and app service plan
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"
myWebAppName=SignalRTestWebApp$randomNum
myAppSvcPlanName=$myAppSvcName"Plan"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 1 \
  --service-mode Default

# Create an App Service plan.
az appservice plan create --name $myAppSvcPlanName --resource-group $myResourceGroupName --sku FREE

# Create the Web App
az webapp create --name $myWebAppName --resource-group $myResourceGroupName --plan $myAppSvcPlanName  

# Get the SignalR primary connection string
primaryConnectionString=$(az signalr key list --name $mySignalRSvcName \
  --resource-group $myResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "Azure:SignalR:ConnectionString=$primaryConnectionString" 

#Add app settings to use with GitHub authentication
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "GitHubClientId=$GitHubClientId" 
az webapp config appsettings set --name $myWebAppName --resource-group $myResourceGroupName \
  --settings "GitHubClientSecret=$GitHubClientSecret" 

# Add the desired deployment user name and password
az webapp deployment user set --user-name $deploymentUser --password $deploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $myWebAppName --resource-group $myResourceGroupName \
  --query [url] -o tsv
```

Noteer de naam die wordt gegenereerd voor de nieuwe resourcegroep. Deze naam wordt weergegeven in de uitvoer. U hebt deze naam nodig als u later alle groepsresources wilt verwijderen.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Uitleg van het script

Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht. In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Hiermee maakt u een resource voor de Azure SignalR-service. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Hiermee vraagt u de sleutels op die door uw toepassing worden gebruikt om realtime updates van inhoud te pushen met SignalR. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Hiermee maakt u een Azure App Service-plan voor het hosten van web-apps. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Hiermee maakt u een Azure-web-app met behulp van het App Service-hostingplan. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Hiermee voegt u nieuwe app-instellingen toe voor de web-app. Deze app-instellingen worden gebruikt voor het opslaan van de SignalR-verbindingsreeks en geheimen van GitHub OAuth-app. |
| [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) | Hiermee werkt u de implementatiereferenties bij. |
| [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) | Hiermee haalt u een URL op voor een eindpunt van de git-opslagplaats voor het klonen en pushen voor de implementatie van de web-app. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure) voor meer informatie over de Azure CLI.

Extra CLI-voorbeeldscripts voor de Azure SignalR-service vindt u in de [documentatie van de Azure SignalR-service](../signalr-reference-cli.md).
