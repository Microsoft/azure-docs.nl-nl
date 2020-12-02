---
title: 'Quickstart: Een Azure API Management-exemplaar maken met behulp van CLI (preview)'
description: Een nieuw API Management-service-exemplaar maken met behulp van Azure CLI.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 863cb6cd10664e14966898045cb2b1861ef75b9f
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887447"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli-preview"></a>Quickstart: Een nieuw API Management-service-exemplaar maken met behulp van Azure CLI (preview)

APIM (API Management) helpt organisaties bij het publiceren van API's naar externe en interne ontwikkelaars, en naar partnerontwikkelaars om alle mogelijkheden van hun gegevens en services te ontsluiten. API Management beschikt over de competenties die belangrijk zijn voor een geslaagd API-programma via ontwikkelaarsbetrokkenheid, zakelijke inzichten, analytische gegevens, beveiliging en bescherming. Met APIM kunt u moderne API-gateways maken en beheren voor bestaande back-endservices die op elke willekeurige locatie worden gehost. Zie het [overzicht](api-management-key-concepts.md) voor meer informatie.

In deze quickstart worden de stappen beschreven voor het maken van een nieuw API Management-exemplaar met behulp van de opdrachten [az apim](/cli/azure/apim) in Azure CLI. De opdrachten in de opdrachtgroep `az apim` zijn momenteel in de preview-fase en zijn mogelijk gewijzigd of verwijderd in een toekomstige release.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.11.1 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Azure API Management-exemplaren moeten, zoals alle Azure-resources, worden geïmplementeerd in een resourcegroep. Met resourcegroepen kunt u gerelateerde Azure-resources organiseren en beheren.

Maak eerst een resourcegroep met de naam *myResourceGroup* in de locatie VS - centraal met behulp van de opdracht [az group create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Een nieuwe service maken

Met deze resourcegroep kunt u een API Management-service-exemplaar maken. Maak er een met behulp van de opdracht [az apim create](/cli/azure/apim#az-apim-create) en geef een servicenaam en de details van de uitgever op. De servicenaam moet uniek zijn binnen Azure. 

In het volgende voorbeeld wordt *myapim* gebruikt voor de servicenaam. Werk de naam bij naar een unieke waarde. Werk ook de naam van de organisatie van de uitgever van de API en het e-mailadres bij om meldingen te ontvangen. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

De opdracht maakt standaard het exemplaar in de ontwikkelaarslaag, een voordelige optie om Azure API Management te evalueren. Deze laag is niet bedoeld voor productie. Zie [bijwerken en schalen](upgrade-and-scale.md) voor meer informatie over het schalen van API Management-lagen. 

> [!TIP]
> Het kan tussen de dertig en veertig minuten duren om een API Management-service in deze laag te maken en activeren. De vorige opdracht gebruikt de optie `--no-wait` zodat de opdracht onmiddellijk retourneert terwijl de service wordt gemaakt.

Controleer de status van de implementatie door de opdracht [az apim show](/cli/azure/apim#az-apim-show) uit te voeren:

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

In eerste instantie ziet de uitvoer er ongeveer zo uit, met de status `Activating`:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Na de activering is de status `Online` en heeft het service-exemplaar een gateway-adres en een openbaar IP-adres. Voorlopig wordt met deze adressen geen inhoud beschikbaar gemaakt. Bijvoorbeeld:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

Als het API Management-service-exemplaar online is, kunt u het gebruiken. Begin met de zelfstudie om [Uw eerste API importeren en publiceren](import-and-publish.md).

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep en de API Management-service-exemplaar te verwijderen, wanneer u ze niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw eerste API importeren en publiceren](import-and-publish.md)
