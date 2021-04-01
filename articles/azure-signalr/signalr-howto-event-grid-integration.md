---
title: Service gebeurtenissen van Azure signalering verzenden naar Event Grid
description: Een hand leiding voor informatie over het inschakelen van Event Grid gebeurtenissen voor uw signalerings service en het verzenden van verbonden/verbroken client verbinding met gebeurtenissen naar een voorbeeld toepassing.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: 84b83c1dd541418c446a89a6f51be668cb41e54e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94562641"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Gebeurtenissen van Azure SignalR Service naar Event Grid verzenden

Azure Event Grid is een volledig beheerde service voor gebeurtenis routering die uniform gebeurtenis verbruik biedt met behulp van een pub-model. In deze hand leiding gebruikt u de Azure CLI om een Azure signalerings service te maken, u te abonneren op verbindings gebeurtenissen en vervolgens een voor beeld-webtoepassing te implementeren om de gebeurtenissen te ontvangen. Ten slotte kunt u verbinding maken en de verbinding verbreken en de nettolading van de gebeurtenis in de voorbeeld toepassing bekijken.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - De Azure CLI-opdrachten in dit artikel zijn ingedeeld voor de **bash** -shell. Als u een andere shell gebruikt, zoals Power shell of opdracht prompt, moet u mogelijk regel voortzettings tekens of variabele toewijzings regels dienovereenkomstig aanpassen. In dit artikel wordt gebruikgemaakt van variabelen om de hoeveelheid opdracht bewerking te minimaliseren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resource groep is een logische container waarin u Azure-resources implementeert en beheert. Met de volgende opdracht [AZ Group Create][az-group-create] wordt een resource groep met de naam *myResourceGroup* gemaakt in de regio *eastus* . Als u een andere naam voor de resource groep wilt gebruiken, stelt u deze `RESOURCE_GROUP_NAME` in op een andere waarde.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Een SignalR-service maken

Implementeer vervolgens een Azure signalerings service in de resource groep met de volgende opdrachten.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Zodra de seingevings service is gemaakt, retourneert de Azure CLI uitvoer die er ongeveer als volgt uitziet:

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

## <a name="create-an-event-endpoint"></a>Een eind punt voor een gebeurtenis maken

In deze sectie gebruikt u een resource manager-sjabloon die zich in een GitHub-opslag plaats bevindt om een vooraf gemaakte voorbeeld webtoepassing te implementeren op Azure App Service. Later abonneert u zich op de Event Grid gebeurtenissen van uw REGI ster en geeft u deze app op als het eind punt waarnaar de gebeurtenissen worden verzonden.

Als u de voor beeld-app wilt implementeren, stelt `SITE_NAME` u een unieke naam in voor de web-app en voert u de volgende opdrachten uit. De site naam moet uniek zijn binnen Azure omdat deze deel uitmaakt van de Fully Qualified Domain Name (FQDN) van de web-app. In een latere sectie gaat u naar de FQDN van de app in een webbrowser om de gebeurtenissen van het REGI ster weer te geven.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Zodra de implementatie is voltooid (dit kan enkele minuten duren), opent u een browser en navigeert u naar uw web-app om te controleren of deze wordt uitgevoerd:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Abonneren op register gebeurtenissen

In Event Grid kunt u zich abonneren op een *onderwerp* om te zien welke gebeurtenissen u wilt bijhouden en waar u ze kunt verzenden. Met de volgende opdracht voor het maken van een [eventgrid-abonnement][az-eventgrid-event-subscription-create] op de Azure signalerings service die u hebt gemaakt, en geeft u de URL van uw web-app op als het eind punt waarnaar gebeurtenissen moeten worden verzonden. De omgevings variabelen die u in eerdere secties hebt ingevuld, worden hier opnieuw gebruikt, dus er zijn geen bewerkingen vereist.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Wanneer het abonnement is voltooid, ziet de uitvoer er ongeveer als volgt uit:

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

## <a name="trigger-registry-events"></a>Register gebeurtenissen activeren

Schakel over naar de service modus `Serverless Mode` en stel een client verbinding met de signalerings service in. U kunt voor [beeld zonder server](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) als referentie nemen.

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

## <a name="view-registry-events"></a>Register gebeurtenissen weer geven

U hebt nu een client verbonden met de signalerings service. Ga naar de Web-App van Event Grid viewer en Bekijk een `ClientConnectionConnected` gebeurtenis. Als u de client beëindigt, wordt er ook een `ClientConnectionDisconnected` gebeurtenis weer geven.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
