---
title: Overzicht van aangepaste providers
description: Meer informatie over aangepaste Azure-resource providers en hoe u het Azure API-vlak kunt uitbreiden zodat het past bij uw werk stromen.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80398463"
---
# <a name="azure-custom-resource-providers-overview"></a>Overzicht van aangepaste Azure-resource providers

Aangepaste Azure-resource providers is een uitbreidbaar platform voor Azure. Hiermee kunt u aangepaste Api's definiëren die kunnen worden gebruikt om de standaard Azure-ervaring te verrijken. Deze documentatie beschrijft:

- Een aangepaste Azure-resource provider bouwen en implementeren.
- Het gebruik van aangepaste Azure-resource providers om bestaande werk stromen uit te breiden.
- Waar u gidsen en code voorbeelden kunt vinden om aan de slag te gaan.

![Overzicht van aangepaste provider](./media/overview/overview.png)

> [!IMPORTANT]
> Aangepaste providers zijn momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="what-can-custom-resource-providers-do"></a>Wat kunnen aangepaste resource providers doen

Hier volgen enkele voor beelden van wat u kunt doen met aangepaste Azure-resource providers:

- Breid Azure Resource Manager REST API uit voor het toevoegen van interne en externe services.
- Schakel aangepaste scenario's in op bestaande Azure-werk stromen.
- Het besturings element en het effect van Azure Resource Manager sjablonen aanpassen.

## <a name="what-is-a-custom-resource-provider"></a>Wat is een aangepaste resource provider?

Aangepaste Azure-resource providers worden gemaakt door een contract te maken tussen Azure en een eind punt. Dit contract definieert een lijst met nieuwe resources en acties via een nieuwe resource, **micro soft. CustomProviders/resourceProviders**. De aangepaste resource provider maakt deze nieuwe Api's vervolgens beschikbaar in Azure. Aangepaste Azure-resource providers bestaan uit drie delen: aangepaste resource provider, **eind punten** en aangepaste resources.

## <a name="how-to-build-custom-resource-providers"></a>Aangepaste resource providers bouwen

Aangepaste resource providers zijn een lijst met contracten tussen Azure en eind punten. In dit contract wordt beschreven hoe Azure moet communiceren met een eind punt. De resource provider fungeert als een proxy, stuurt aanvragen en antwoorden van en naar het opgegeven **eind punt**. Een resource provider kan twee soorten contracten opgeven: [**resourceTypes**](./custom-providers-resources-endpoint-how-to.md) en [**Actions**](./custom-providers-action-endpoint-how-to.md). Deze zijn ingeschakeld via eindpunt definities. Een eindpunt definitie bestaat uit drie velden: **naam**, **routingType** en **eind punt**.

Voor beeld-eind punt:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Eigenschap | Vereist | Beschrijving
---|---|---
naam | *klikt* | De naam van de eindpuntdefinitie. Azure geeft deze naam beschikbaar via de API onder/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *geen* | Bepaalt het contract type met het **eind punt**. Als u niets opgeeft, wordt het standaard ingesteld op proxy.
endpoint | *klikt* | Het eindpunt waarnaar de aanvragen worden gerouteerd. Hiermee worden de reactie en eventuele neven effecten van de aanvraag verwerkt.

### <a name="building-custom-resources"></a>Aangepaste resources bouwen

**ResourceTypes** beschrijven nieuwe aangepaste resources die worden toegevoegd aan Azure. Deze beschikken over elementaire, geruste ruwe methoden. [Meer informatie over het maken van aangepaste resources](./custom-providers-resources-endpoint-how-to.md)

Voor beeld van een aangepaste resource provider met **resourceTypes**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Api's die zijn toegevoegd aan Azure voor het bovenstaande voor beeld:

HttpMethod | Voor beeld-URI | Beschrijving
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018-09 -01-preview | De Azure REST API-aanroep voor het maken van een nieuwe resource.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018-09 -01-preview | De Azure REST API-aanroep voor het verwijderen van een bestaande resource.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018-09 -01-preview | De Azure REST API-aanroep om een bestaande resource op te halen.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources? API-Version = 2018-9 -01-preview | De Azure REST API-aanroep voor het ophalen van de lijst met bestaande resources.

### <a name="building-custom-actions"></a>Aangepaste acties bouwen

**Acties** beschrijven nieuwe acties die aan Azure worden toegevoegd. Deze kunnen worden weer gegeven boven op de resource provider of genest zijn onder een resource **type**. [Meer informatie over het maken van aangepaste acties](./custom-providers-action-endpoint-how-to.md)

Voor beeld van een aangepaste resource provider met **acties**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Api's die zijn toegevoegd aan Azure voor het bovenstaande voor beeld:

HttpMethod | Voor beeld-URI | Beschrijving
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction? API-Version = 2018-9 -01-preview | De aanroep van Azure REST API om de actie te activeren.

## <a name="looking-for-help"></a>Hulp nodig?

Als u vragen hebt over de ontwikkeling van de aangepaste Azure-resource provider, kunt u de vragen stellen op [stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Misschien is er een soortgelijke vraag gesteld en beantwoord, dus controleer dit eerst vóór u een vraag stelt. Voeg de tag ```azure-custom-providers``` toe om snel antwoord te krijgen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over aangepaste providers. Ga naar het volgende artikel om een aangepaste provider te maken.

- [Snelstartgids: een aangepaste Azure-resource provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Zelf studie: aangepaste acties en resources maken in azure](./tutorial-get-started-with-custom-providers.md)
- [Procedure: aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Procedure: aangepaste resources toevoegen aan Azure REST API](./custom-providers-resources-endpoint-how-to.md)
