---
title: Webhooks om te reageren op register acties
description: Meer informatie over het gebruik van webhooks om gebeurtenissen te activeren wanneer push-of pull-acties worden uitgevoerd in uw register opslagplaatsen.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5374b58ba72727500294a173c26e9a131b29fe34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101722242"
---
# <a name="using-azure-container-registry-webhooks"></a>Azure Container Registry-webhooks gebruiken

Een Azure-containerregister slaat persoonlijke Docker-containerinstallatiekopieën op en beheert ze, vergelijkbaar met de manier waarop Docker Hub openbare Docker-installatiekopieën opslaat. Het kan ook opslag plaatsen hosten voor [helm-grafieken](container-registry-helm-repos.md) (preview), een verpakkings indeling voor het implementeren van toepassingen naar Kubernetes. U kunt webhooks gebruiken om gebeurtenissen te activeren wanneer bepaalde acties in een van uw register opslagplaatsen worden uitgevoerd. Webhooks kunnen reageren op gebeurtenissen op het register niveau of de scope kan worden beperkt tot een specifieke opslagplaats label. Met een  [geo-gerepliceerd](container-registry-geo-replication.md) REGI ster configureert u elke webhook om te reageren op gebeurtenissen in een specifieke regionale replica.

Het eind punt voor een webhook moet openbaar toegankelijk zijn vanuit het REGI ster. U kunt register-webhook-aanvragen configureren om te verifiëren bij een beveiligd eind punt.

Zie [Azure container Registry webhook-schema verwijzing](container-registry-webhook-reference.md)voor meer informatie over webhook-aanvragen.

## <a name="prerequisites"></a>Vereisten

* Azure-containerregister: maak een containerregister in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure Portal](container-registry-get-started-portal.md) of de [Azure cli](container-registry-get-started-azure-cli.md). De [Azure container Registry-service lagen](container-registry-skus.md) hebben verschillende-quota voor webhooks.
* Docker-CLI: installeer de [Docker-engine](https://docs.docker.com/engine/installation/) om uw lokale computer als een Docker-host in te stellen en de Docker-CLI-opdrachten te gebruiken.

## <a name="create-webhook---azure-portal"></a>Webhook-Azure Portal maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer naar het container register waarin u een webhook wilt maken.
1. Onder **Services** selecteert u **webhooks**.
1. Selecteer **toevoegen** op de werk balk van de webhook.
1. Voltooi het formulier *webhook maken* met de volgende gegevens:

| Waarde | Beschrijving |
|---|---|
| Naam webhook | De naam die u aan de webhook wilt geven. Het mag alleen letters en cijfers bevatten en moet 5-50 tekens lang zijn. |
| Locatie | Voor een [geo-gerepliceerd](container-registry-geo-replication.md) REGI ster geeft u de Azure-regio van de register replica op. 
| Service-URI | De URI waar de webhook POST meldingen moet verzenden. |
| Aangepaste headers | Kopteksten die u wilt door geven samen met de POST-aanvraag. Ze moeten de indeling ' sleutel: waarde ' hebben. |
| Trigger acties | Acties waarmee de webhook wordt geactiveerd. Acties zijn onder andere afbeeldings pushen, afbeeldingen verwijderen, helm-grafiek pushen, helm-grafieken verwijderen en afbeeldings quarantaine. U kunt een of meer acties kiezen om de webhook te activeren. |
| Status | De status van de webhook nadat deze is gemaakt. Het is standaard ingeschakeld. |
| Bereik | Het bereik waarmee de webhook werkt. Als u niets opgeeft, geldt het bereik voor alle gebeurtenissen in het REGI ster. Het kan worden opgegeven voor een opslag plaats of een tag met de indeling ' opslag plaats: tag ' of ' opslag plaats: * ' voor alle labels onder een opslag plaats. |

Voor beeld van webhook-formulier:

![Scherm opname waarin de ACR-webhook wordt gemaakt en U in de Azure Portal.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Webhook maken-Azure CLI

Als u een webhook wilt maken met behulp van de Azure CLI, gebruikt u de opdracht [AZ ACR webhook Create](/cli/azure/acr/webhook#az-acr-webhook-create) . Met de volgende opdracht maakt u een webhook voor alle installatie kopieën gebeurtenissen verwijderen in het REGI ster *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook testen

### <a name="azure-portal"></a>Azure Portal

Voordat u de webhook gebruikt, kunt u deze testen met de knop **ping** . Ping verzendt een algemene POST-aanvraag naar het opgegeven eind punt en registreert het antwoord. Met de functie Ping kunt u controleren of u de webhook juist hebt geconfigureerd.

1. Selecteer de webhook die u wilt testen.
2. Selecteer **ping** in de bovenste werk balk.
3. Controleer het antwoord van het eind punt in de kolom **HTTP-status** .

![Gebruikers interface voor het maken van ACR-webhooks in de Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Als u een ACR-webhook met de Azure CLI wilt testen, gebruikt u de opdracht [AZ ACR webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) .

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Als u de resultaten wilt weer geven, gebruikt u de opdracht [AZ ACR webhook List-Events](/cli/azure/acr/webhook) .

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook verwijderen

### <a name="azure-portal"></a>Azure Portal

Elke webhook kan worden verwijderd door de webhook te selecteren en vervolgens de knop **verwijderen** in de Azure Portal.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Volgende stappen

### <a name="webhook-schema-reference"></a>Webhook-schema verwijzing

Zie de verwijzing naar het webhook-schema voor meer informatie over de indeling en eigenschappen van de nettoladingen van JSON-gebeurtenissen die door Azure Container Registry worden gegenereerd:

[Azure Container Registry webhook-schema verwijzing](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid gebeurtenissen

Naast de systeem eigen register webhook-gebeurtenissen die in dit artikel worden besproken, kunnen Azure Container Registry gebeurtenissen naar Event Grid verzenden:

[Quick Start: container register gebeurtenissen naar Event Grid verzenden](container-registry-event-grid-quickstart.md)
