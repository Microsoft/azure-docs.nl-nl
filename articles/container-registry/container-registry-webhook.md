---
title: Webhooks om te reageren op registeracties
description: Meer informatie over het gebruik van webhooks om gebeurtenissen te activeren wanneer push- of pull-acties plaatsvinden in uw register-opslagplaatsen.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 4f6fb719f8d9d51429a19616aa5548b32a2687e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773396"
---
# <a name="using-azure-container-registry-webhooks"></a>Webhooks Azure Container Registry gebruiken

Een Azure-containerregister slaat persoonlijke Docker-containerinstallatiekopieën op en beheert ze, vergelijkbaar met de manier waarop Docker Hub openbare Docker-installatiekopieën opslaat. Het kan ook opslagplaatsen hosten voor [Helm-grafieken](container-registry-helm-repos.md) (preview), een pakketindeling voor het implementeren van toepassingen in Kubernetes. U kunt webhooks gebruiken om gebeurtenissen te activeren wanneer bepaalde acties plaatsvinden in een van uw register-opslagplaatsen. Webhooks kunnen reageren op gebeurtenissen op registerniveau of ze kunnen worden beperkt tot een specifieke opslagplaatstag. Met een  [geo-gerepliceerd](container-registry-geo-replication.md) register configureert u elke webhook om te reageren op gebeurtenissen in een specifieke regionale replica.

Het eindpunt voor een webhook moet openbaar toegankelijk zijn vanuit het register. U kunt registerwebhookaanvragen configureren om te verifiëren bij een beveiligd eindpunt.

Zie Azure Container Registry webhookschemareferentie voor meer informatie over [webhookaanvragen.](container-registry-webhook-reference.md)

## <a name="prerequisites"></a>Vereisten

* Azure-containerregister: maak een containerregister in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure Portal](container-registry-get-started-portal.md) of [de Azure CLI.](container-registry-get-started-azure-cli.md) De [Azure Container Registry servicelagen hebben](container-registry-skus.md) verschillende quota voor webhooks.
* Docker-CLI: installeer de [Docker-engine](https://docs.docker.com/engine/installation/) om uw lokale computer als een Docker-host in te stellen en de Docker-CLI-opdrachten te gebruiken.

## <a name="create-webhook---azure-portal"></a>Webhook maken - Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer naar het containerregister waarin u een webhook wilt maken.
1. Selecteer  **webhooks onder Services.**
1. Selecteer **Toevoegen** op de webhookwerkbalk.
1. Vul het *formulier Webhook maken* in met de volgende informatie:

| Waarde | Beschrijving |
|---|---|
| Webhooknaam | De naam die u aan de webhook wilt geven. Het mag alleen letters en cijfers bevatten en moet 5 tot 50 tekens lang zijn. |
| Locatie | Geef voor [een geo-gerepliceerd](container-registry-geo-replication.md) register de Azure-regio van de registerreplica op. 
| Service-URI | De URI waar de webhook POST-meldingen moet verzenden. |
| Aangepaste headers | Headers die u wilt doorgeven samen met de POST-aanvraag. Deze moeten de indeling 'sleutel: waarde' hebben. |
| Triggeracties | Acties die de webhook activeren. Acties zijn onder andere het pushen van afbeeldingen, het verwijderen van afbeeldingen, het pushen van helm-grafieken, het verwijderen van Helm-grafieken en het in quarantaine plaatsen van afbeeldingen. U kunt een of meer acties kiezen om de webhook te activeren. |
| Status | De status van de webhook nadat deze is gemaakt. Deze is standaard ingeschakeld. |
| Bereik | Het bereik waarop de webhook werkt. Als dit niet wordt opgegeven, is het bereik voor alle gebeurtenissen in het register. Deze kan worden opgegeven voor een opslagplaats of tag met behulp van de indeling 'repository:tag' of 'repository:*' voor alle tags in een opslagplaats. |

Voorbeeld van een webhookformulier:

![Schermopname van het maken van de ACR-webhook in de Azure Portal.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Webhook maken - Azure CLI

Gebruik de opdracht [az acr webhook create](/cli/azure/acr/webhook#az_acr_webhook_create) om een webhook te maken met behulp van de Azure CLI. Met de volgende opdracht maakt u een webhook voor alle gebeurtenissen voor het verwijderen van afbeeldingen in het register *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook testen

### <a name="azure-portal"></a>Azure Portal

Voordat u de webhook gebruikt, kunt u deze testen met de **knop Ping.** Ping verzendt een algemene POST-aanvraag naar het opgegeven eindpunt en registreert het antwoord. Met behulp van de ping-functie kunt u controleren of u de webhook correct hebt geconfigureerd.

1. Selecteer de webhook die u wilt testen.
2. Selecteer Ping in de bovenste **werkbalk.**
3. Controleer het antwoord van het eindpunt in de **kolom HTTP STATUS.**

![Gebruikersinterface voor het maken van een ACR-webhook in de Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Als u een ACR-webhook wilt testen met de Azure CLI, gebruikt u [de opdracht az acr webhook ping.](/cli/azure/acr/webhook#az_acr_webhook_ping)

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Gebruik de opdracht [az acr webhook list-events](/cli/azure/acr/webhook) om de resultaten te bekijken.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook verwijderen

### <a name="azure-portal"></a>Azure Portal

Elke webhook kan worden verwijderd door de webhook te selecteren en vervolgens de knop Verwijderen in de Azure Portal. 

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Volgende stappen

### <a name="webhook-schema-reference"></a>Naslag voor webhookschema

Zie de naslaginformatie over het webhookschema voor meer informatie over de indeling en eigenschappen van de nettoladingen van de JSON-gebeurtenissen die door Azure Container Registry worden weergegeven:

[Azure Container Registry webhookschema](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid gebeurtenissen

Naast de systeemeigen webhookgebeurtenissen die in dit artikel worden besproken, kunnen Azure Container Registry gebeurtenissen naar de Event Grid:

[Quickstart: Containerregistergebeurtenissen verzenden naar Event Grid](container-registry-event-grid-quickstart.md)
