---
title: 'Quick Start: gebeurtenissen verzenden naar Event Grid'
description: In deze Quick Start schakelt u Event Grid-gebeurtenissen in voor het container register en verzendt u vervolgens de push-installatie kopie van de container en gebeurtenissen naar een voorbeeld toepassing.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2d13dd0ec5e50086e674b215d93917d6173d5af9
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694398"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Quick Start: gebeurtenissen verzenden vanuit een persoonlijk container register naar Event Grid

Azure Event Grid is een volledig beheerde service voor gebeurtenis routering die uniform gebeurtenis verbruik biedt met behulp van een model voor publiceren en abonneren. In deze Quick Start gebruikt u de Azure CLI om een container register te maken, u te abonneren op register gebeurtenissen en vervolgens een voor beeld-webtoepassing te implementeren om de gebeurtenissen te ontvangen. Ten slotte kunt u container installatie kopie `push` en `delete` gebeurtenissen activeren en de nettolading van de gebeurtenis in de voorbeeld toepassing bekijken.

Nadat u de stappen in dit artikel hebt voltooid, worden gebeurtenissen die vanuit het container register naar Event Grid worden verzonden, weer gegeven in de voor beeld-web-app:

![Webbrowser waarin de voorbeeld webtoepassing wordt weer gegeven met drie ontvangen gebeurtenissen][sample-app-01]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- De Azure CLI-opdrachten in dit artikel zijn ingedeeld voor de **bash** -shell. Als u een andere shell gebruikt, zoals Power shell of opdracht prompt, moet u mogelijk regel voortzettings tekens of variabele toewijzings regels dienovereenkomstig aanpassen. In dit artikel wordt gebruikgemaakt van variabelen om de hoeveelheid opdracht bewerking te minimaliseren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resource groep is een logische container waarin u Azure-resources implementeert en beheert. Met de volgende opdracht [AZ Group Create][az-group-create] wordt een resource groep met de naam *myResourceGroup* gemaakt in de regio *eastus* . Als u een andere naam voor de resource groep wilt gebruiken, stelt u deze `RESOURCE_GROUP_NAME` in op een andere waarde.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Een containerregister maken

Implementeer vervolgens een container register in de resource groep met de volgende opdrachten. Voordat u de opdracht [AZ ACR Create][az-acr-create] uitvoert, stelt `ACR_NAME` u een naam in voor het REGI ster. De naam moet uniek zijn binnen Azure en mag Maxi maal 5-50 alfanumerieke tekens bevatten.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Zodra het REGI ster is gemaakt, retourneert de Azure CLI uitvoer die er ongeveer als volgt uitziet:

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

U ziet dat de voor beeld-app wordt gerenderd zonder gebeurtenis berichten weer gegeven:

![Webbrowser die de voor beeld-web-app zonder weer gegeven gebeurtenissen weergeeft][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Abonneren op register gebeurtenissen

In Event Grid kunt u zich abonneren op een *onderwerp* om te zien welke gebeurtenissen u wilt bijhouden en waar u ze kunt verzenden. Met de volgende opdracht voor het maken van een abonnement op het [eventgrid van gebeurtenis abonnementen][az-eventgrid-event-subscription-create] wordt het container register dat u hebt gemaakt, en wordt de URL van uw web-app opgegeven als het eind punt waarnaar gebeurtenissen moeten worden verzonden. De omgevings variabelen die u in eerdere secties hebt ingevuld, worden hier opnieuw gebruikt, dus er zijn geen bewerkingen vereist.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Wanneer het abonnement is voltooid, ziet de uitvoer er ongeveer als volgt uit:

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

## <a name="trigger-registry-events"></a>Register gebeurtenissen activeren

Nu de voor beeld-app actief is en u bent geabonneerd op uw REGI ster met Event Grid, bent u klaar om een aantal gebeurtenissen te genereren. In deze sectie gebruikt u ACR-taken voor het maken en pushen van een container installatie kopie naar het REGI ster. ACR-taken is een functie van Azure Container Registry waarmee u container installatie kopieën in de cloud kunt maken, zonder dat de docker-engine moet zijn geïnstalleerd op uw lokale computer.

### <a name="build-and-push-image"></a>Installatie kopie maken en pushen

Voer de volgende Azure CLI-opdracht uit om een container installatie kopie te bouwen op basis van de inhoud van een GitHub-opslag plaats. Standaard worden met ACR-taken automatisch een installatie kopie naar het REGI ster geconstrueerd, waardoor de gebeurtenis wordt gegenereerd `ImagePushed` .

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

U ziet een uitvoer die vergelijkbaar is met de volgende wanneer ACR-taken worden gebouwd en vervolgens uw installatie kopie pusht. De volgende voorbeeld uitvoer is afgebroken voor de boog.

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

Als u wilt controleren of de gemaakte installatie kopie zich in het REGI ster bevindt, voert u de volgende opdracht uit om de tags in de opslag plaats ' myimage ' weer te geven:

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

De "v1"-tag van de installatie kopie die u hebt gemaakt, moet worden weer gegeven in de uitvoer, vergelijkbaar met het volgende:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>De installatie kopie verwijderen

Genereer nu een `ImageDeleted` gebeurtenis door de installatie kopie te verwijderen met de opdracht [AZ ACR repository delete][az-acr-repository-delete] :

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

De uitvoer ziet er ongeveer als volgt uit, waarbij u wordt gevraagd om het manifest en de bijbehorende afbeeldingen te verwijderen:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Register gebeurtenissen weer geven

U hebt nu een installatie kopie naar het REGI ster gepusht en vervolgens verwijderd. Navigeer naar uw Event Grid Viewer-web-app en u moet beide `ImageDeleted` en `ImagePushed` gebeurtenissen zien. U ziet mogelijk ook een validatie gebeurtenis voor een abonnement die wordt gegenereerd door de opdracht uit te voeren in het gedeelte [Abonneren op register gebeurtenissen](#subscribe-to-registry-events) .

De volgende scherm afbeelding toont de voor beeld-app met de drie gebeurtenissen en de `ImageDeleted` gebeurtenis wordt uitgevouwen om de details ervan weer te geven.

![Webbrowser met de voor beeld-app met ImagePushed-en ImageDeleted-gebeurtenissen][sample-app-03]

Gefeliciteerd. Als u de `ImagePushed` -en `ImageDeleted` -gebeurtenissen ziet, verzendt uw REGI ster gebeurtenissen naar Event grid en stuurt Event grid deze gebeurtenissen door naar het eind punt van uw web-app.

## <a name="clean-up-resources"></a>Resources opschonen

Zodra u klaar bent met de resources die u in deze Quick Start hebt gemaakt, kunt u ze allemaal verwijderen met de volgende Azure CLI-opdracht. Wanneer u een resource groep verwijdert, worden alle resources die deze bevat permanent verwijderd.

**Waarschuwing**: deze bewerking kan niet ongedaan worden gemaakt. Zorg ervoor dat u geen van de resources in de groep meer nodig hebt voordat u de opdracht uitvoert.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Event Grid-gebeurtenisschema

U vindt de Azure Container Registry gebeurtenis bericht schema verwijzing in de Event Grid documentatie:

[Azure Event Grid-gebeurtenis schema voor Container Registry](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een container register geïmplementeerd, een installatie kopie gemaakt met ACR-taken, het bestand verwijderd en de gebeurtenissen van het REGI ster verbruikt van Event Grid met een voorbeeld toepassing. Ga vervolgens naar de zelf studie ACR-taken voor meer informatie over het bouwen van container installatie kopieën in de Cloud, inclusief geautomatiseerde builds van de update van de basis installatie kopie:

> [!div class="nextstepaction"]
> [Container installatie kopieën bouwen in de Cloud met ACR-taken](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
