---
title: 'Quickstart: Gebeurtenissen verzenden naar Event Grid'
description: In deze quickstart gaat u Event Grid voor uw containerregister inschakelen en vervolgens push- en verwijdergebeurtenissen voor containerafbeeldingen verzenden naar een voorbeeldtoepassing.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 43dea2640c9c9445ea464205f6c586bc1e486206
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784021"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Quickstart: Gebeurtenissen verzenden van een privécontainerregister naar Event Grid

Azure Event Grid is een volledig beheerde service voor gebeurtenisroutering die uniform gebeurtenisverbruik biedt met behulp van een model voor publiceren/abonneren. In deze quickstart gebruikt u de Azure CLI om een containerregister te maken, u te abonneren op registergebeurtenissen en vervolgens een voorbeeldwebtoepassing te implementeren om de gebeurtenissen te ontvangen. Ten slotte activeert u `push` containerafbeeldingen en gebeurtenissen en `delete` bekijkt u de nettolading van de gebeurtenis in de voorbeeldtoepassing.

Nadat u de stappen in dit artikel hebt voltooid, worden gebeurtenissen die vanuit uw containerregister naar Event Grid weergegeven in de voorbeeld-web-app:

![Webbrowser met drie ontvangen gebeurtenissen voor het weergeven van de voorbeeldwebtoepassing][sample-app-01]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- De Azure CLI-opdrachten in dit artikel zijn opgemaakt voor de **Bash-shell.** Als u een andere shell gebruikt, zoals PowerShell of de opdrachtprompt, moet u mogelijk regelcontanttekens of variabele toewijzingsregels dienovereenkomstig aanpassen. In dit artikel worden variabelen gebruikt om het aantal vereiste bewerkingen van opdrachten te minimaliseren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin u uw Azure-resources implementeert en beheert. Met de [volgende opdracht az group create][az-group-create] maakt u een resourcegroep met de naam *myResourceGroup* in de *regio eastus.* Als u een andere naam voor uw resourcegroep wilt gebruiken, stelt u in `RESOURCE_GROUP_NAME` op een andere waarde.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Een containerregister maken

Implementeer vervolgens een containerregister in de resourcegroep met de volgende opdrachten. Voordat u de opdracht [az acr create gaat][az-acr-create] uitvoeren, stelt u in op een naam voor het `ACR_NAME` register. De naam moet uniek zijn binnen Azure en mag niet meer dan 5 tot 50 alfanumerieke tekens bevatten.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Zodra het register is gemaakt, retourneert de Azure CLI uitvoer die vergelijkbaar is met de volgende:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```

## <a name="create-an-event-endpoint"></a>Een gebeurtenis-eindpunt maken

In deze sectie gebruikt u een sjabloon Resource Manager in een GitHub-opslagplaats om een vooraf gebouwde voorbeeldwebtoepassing te implementeren voor Azure App Service. Later abonneert u zich op de Event Grid van uw register en geeft u deze app op als het eindpunt waarop de gebeurtenissen worden verzonden.

Als u de voorbeeld-app wilt implementeren, stelt `SITE_NAME` u in op een unieke naam voor uw web-app en voert u de volgende opdrachten uit. De sitenaam moet uniek zijn binnen Azure omdat deze deel uitmaakt van de FQDN (Fully Qualified Domain Name) van de web-app. In een latere sectie gaat u naar de FQDN van de app in een webbrowser om de gebeurtenissen van uw register weer te geven.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Zodra de implementatie is geslaagd (dit kan enkele minuten duren), opent u een browser en navigeert u naar uw web-app om te zien of deze wordt uitgevoerd:

`http://<your-site-name>.azurewebsites.net`

Als het goed is, wordt de voorbeeld-app weergegeven zonder dat er gebeurtenisberichten worden weergegeven:

![Webbrowser met voorbeeldweb-app zonder weergegeven gebeurtenissen][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Abonneren op registergebeurtenissen

In Event Grid u zich op een *onderwerp* om aan te geven welke gebeurtenissen u wilt bijhouden en waar u ze wilt verzenden. Met de [volgende opdracht az eventgrid event-subscription create][az-eventgrid-event-subscription-create] abonneert u zich op het containerregister dat u hebt gemaakt en geeft u de URL van uw web-app op als het eindpunt waarop gebeurtenissen moeten worden verzenden. De omgevingsvariabelen die u in eerdere secties hebt ingevuld, worden hier opnieuw gebruikt, dus er zijn geen bewerkingen vereist.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Wanneer het abonnement is voltooid, ziet u uitvoer die er ongeveer als volgt uit ziet:

```json
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Registergebeurtenissen activeren

Nu de voorbeeld-app actief is en u zich hebt geabonneerd op uw register met Event Grid, bent u klaar om een aantal gebeurtenissen te genereren. In deze sectie gebruikt u ACR-taken om een containerafbeelding te bouwen en naar uw register te pushen. ACR-taken is een functie van Azure Container Registry waarmee u containerafbeeldingen kunt bouwen in de cloud, zonder dat de Docker-engine op uw lokale computer hoeft te zijn geïnstalleerd.

### <a name="build-and-push-image"></a>Build- en push-afbeelding

Voer de volgende Azure CLI-opdracht uit om een containerafbeelding te maken op basis van de inhoud van een GitHub-opslagplaats. Standaard pusht ACR-taken automatisch een met succes gebouwde afbeelding naar uw register, waardoor de gebeurtenis wordt `ImagePushed` gegenereerd.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

De uitvoer moet er ongeveer als volgt uit zien wanneer ACR-taken en vervolgens uw afbeelding pusht. De volgende voorbeelduitvoer is ingekort.

```output
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Voer de volgende opdracht uit om de tags in de opslagplaats 'myimage' weer te geven om te controleren of de gebouwde afbeelding zich in uw register staat:

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

De tag 'v1' van de afbeelding die u hebt gemaakt, moet worden weergegeven in de uitvoer, vergelijkbaar met de volgende:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>De afbeelding verwijderen

Genereer nu een gebeurtenis door de afbeelding te verwijderen met de `ImageDeleted` [opdracht az acr repository delete:][az-acr-repository-delete]

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

De uitvoer moet er ongeveer als volgt uit zien, waarin u wordt gevraagd om bevestiging om het manifest en de bijbehorende afbeeldingen te verwijderen:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Registergebeurtenissen weergeven

U hebt nu een afbeelding naar het register ge pusht en deze vervolgens verwijderd. Navigeer naar Event Grid Viewer-web-app en u ziet nu `ImageDeleted` zowel - als `ImagePushed` -gebeurtenissen. Mogelijk ziet u ook een validatiegebeurtenis voor het abonnement die wordt gegenereerd door de opdracht uit te voeren in de sectie [Abonneren op registergebeurtenissen.](#subscribe-to-registry-events)

In de volgende schermopname ziet u de voorbeeld-app met de drie gebeurtenissen en wordt de gebeurtenis uitgebreid `ImageDeleted` om de details ervan weer te geven.

![Webbrowser met de voorbeeld-app met de gebeurtenissen ImagePushed en ImageDeleted][sample-app-03]

Gefeliciteerd Als u de gebeurtenissen en ziet, stuurt uw register gebeurtenissen naar Event Grid en Event Grid deze gebeurtenissen doorgestuurd naar het eindpunt van `ImagePushed` `ImageDeleted` uw web-app.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de resources die u in deze quickstart hebt gemaakt, kunt u ze allemaal verwijderen met de volgende Azure CLI-opdracht. Wanneer u een resourcegroep verwijdert, worden alle resources die deze bevat permanent verwijderd.

**WAARSCHUWING:** deze bewerking kan niet ongedaan worden uitgevoerd. Zorg ervoor dat u de resources in de groep niet meer nodig hebt voordat u de opdracht gaat uitvoeren.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Event Grid-gebeurtenisschema

U vindt de naslag voor Azure Container Registry gebeurtenisberichtschema in de Event Grid documentatie:

[Azure Event Grid gebeurtenisschema voor Container Registry](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een containerregister geïmplementeerd, een installatielijst gemaakt met ACR-taken, deze verwijderd en de gebeurtenissen van uw register uit Event Grid gebruikt met een voorbeeldtoepassing. Ga vervolgens verder met de zelfstudie ACR-taken over het bouwen van containerafbeeldingen in de cloud, inclusief geautomatiseerde builds bij het bijwerken van basisafbeeldingen:

> [!div class="nextstepaction"]
> [Containerafbeeldingen bouwen in de cloud met ACR-taken](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create
[az-group-create]: /cli/azure/group#az_group_create
