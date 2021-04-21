---
title: Informatie over het verzenden Azure SignalR Service gebeurtenissen naar Event Grid
description: Een handleiding om u te laten zien hoe u Event Grid voor uw SignalR Service kunt inschakelen en vervolgens verbonden/niet-verbonden clientgebeurtenissen naar een voorbeeldtoepassing kunt verzenden.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: 65fb54dbfc5158ef8cc2b488f267c92f601502f6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784583"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Gebeurtenissen van Azure SignalR Service naar Event Grid verzenden

Azure Event Grid is een volledig beheerde service voor gebeurtenisroutering die uniform gebeurtenisverbruik biedt met behulp van een pub-submodel. In deze handleiding gebruikt u de Azure CLI om een Azure SignalR Service te maken, u te abonneren op verbindingsgebeurtenissen en vervolgens een voorbeeldwebtoepassing te implementeren om de gebeurtenissen te ontvangen. Ten slotte kunt u verbinding maken en de verbinding verbreken en de nettolading van de gebeurtenis in de voorbeeldtoepassing bekijken.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - De Azure CLI-opdrachten in dit artikel zijn opgemaakt voor de **Bash-shell.** Als u een andere shell gebruikt, zoals PowerShell of de opdrachtprompt, moet u mogelijk de tekens voor het vervolg van de regel of de toewijzingsregels van variabelen dienovereenkomstig aanpassen. In dit artikel worden variabelen gebruikt om het aantal vereiste bewerkingen van opdrachten te minimaliseren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin u uw Azure-resources implementeert en beheert. Met de [volgende opdracht az group create][az-group-create] maakt u een resourcegroep met de naam *myResourceGroup* in de *regio eastus.* Als u een andere naam voor uw resourcegroep wilt gebruiken, stelt u `RESOURCE_GROUP_NAME` in op een andere waarde.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Een SignalR-service maken

Implementeer vervolgens een Azure Signalr-service in de resourcegroep met de volgende opdrachten.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Zodra de SignalR Service is gemaakt, retourneert de Azure CLI uitvoer die er ongeveer als volgt uit zien:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Een gebeurtenis-eindpunt maken

In deze sectie gebruikt u een sjabloon Resource Manager in een GitHub-opslagplaats om een vooraf gebouwde voorbeeldwebtoepassing te implementeren op Azure App Service. Later abonneert u zich op de gebeurtenissen van Event Grid register en geeft u deze app op als het eindpunt waarop de gebeurtenissen worden verzonden.

Als u de voorbeeld-app wilt implementeren, stelt u in op `SITE_NAME` een unieke naam voor uw web-app en voert u de volgende opdrachten uit. De sitenaam moet uniek zijn binnen Azure omdat deze deel uitmaakt van de FQDN (Fully Qualified Domain Name) van de web-app. In een latere sectie gaat u naar de FQDN van de app in een webbrowser om de gebeurtenissen van uw register weer te geven.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Zodra de implementatie is geslaagd (dit kan enkele minuten duren), opent u een browser en navigeert u naar uw web-app om te zien of deze wordt uitgevoerd:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Abonneren op registergebeurtenissen

In Event Grid u zich op een *onderwerp* om aan te geven welke gebeurtenissen u wilt bijhouden en waar u ze wilt verzenden. Met de [volgende opdracht az eventgrid event-subscription create][az-eventgrid-event-subscription-create] abonneert u zich op de Azure SignalR Service die u hebt gemaakt, en geeft u de URL van uw web-app op als het eindpunt waarop gebeurtenissen moeten worden verzenden. De omgevingsvariabelen die u in eerdere secties hebt ingevuld, worden hier opnieuw gebruikt, dus er zijn geen bewerkingen vereist.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Wanneer het abonnement is voltooid, ziet u uitvoer die er ongeveer als volgt uit ziet:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Registergebeurtenissen activeren

Schakel over naar de servicemodus en `Serverless Mode` stel een clientverbinding met de SignalR Service. U kunt [serverloos voorbeeld gebruiken](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) als referentie.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Registergebeurtenissen weergeven

U hebt nu een client verbonden met de SignalR Service. Navigeer naar Event Grid viewer-web-app en u ziet een `ClientConnectionConnected` gebeurtenis. Als u de client beëindigt, ziet u ook een `ClientConnectionDisconnected` gebeurtenis.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create
[az-group-create]: /cli/azure/group#az_group_create
