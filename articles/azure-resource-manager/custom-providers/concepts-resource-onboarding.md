---
title: Onboarding van resources
description: Meer informatie over het voorbereiden van resources met behulp van aangepaste Azure-providers om beheer of configuratie toe te passen op andere Azure-resource typen.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650407"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Overzicht van de resource voor het onboarding van Azure Custom providers

Het onboarden van resources van Azure Custom providers is een uitbreidings model voor Azure-resource typen. Zo kunt u bewerkingen of beheer Toep assen op bestaande Azure-resources op schaal. Zie How to extend Azure [Custom providers Azure](overview.md)voor meer informatie. In dit artikel wordt het volgende beschreven:

- Wat voor onboarding van resources kan doen.
- Basis beginselen voor het onboarding van resources en hoe u deze kunt gebruiken.
- Waar u gidsen en code voorbeelden kunt vinden om aan de slag te gaan.

> [!IMPORTANT]
> Aangepaste providers zijn momenteel beschikbaar als open bare preview.
> Deze preview-versie is beschikbaar zonder een service overeenkomst en wij raden deze niet aan voor productie werkbelastingen. Bepaalde functies worden mogelijk niet ondersteund of hebben mogelijk beperkte mogelijkheden.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="what-can-resource-onboarding-do"></a>Wat kan onboarding van resources doen?

Net als bij [aangepaste Azure-providers](./custom-providers-resources-endpoint-how-to.md), wordt in de bron onboarding een contract gedefinieerd waarmee aanvragen voor het uitvoeren van een proxy naar een eind punt worden gestart. In tegens telling tot aangepaste resources maakt resource-onboarding geen nieuw resource type. In plaats daarvan kan de uitbrei ding van bestaande resource typen worden toegestaan. En de voor bereiding van resources werkt met Azure Policy, zodat het beheer en de configuratie van resources op schaal kunnen worden uitgevoerd. Enkele voor beelden van werk stromen voor onboarding van resources:

- Installeren en beheren op virtuele-machine uitbreidingen.
- Upload en configureer standaard instellingen voor Azure Storage-accounts.
- De basis instellingen voor diagnostische gegevens op schaal inschakelen.

## <a name="resource-onboarding-basics"></a>Basis beginselen van resources voor onboarding

U configureert het voorbereiden van resources via aangepaste Azure-providers met behulp van de resource typen micro soft. CustomProviders/resourceProviders en micro soft. CustomProviders/Associations. Als u het voorbereiden van resources voor een aangepaste provider wilt inschakelen, maakt u tijdens het configuratie proces een **resource type** met de naam ' Associations ' met een **RoutingType** dat ' extension ' bevat. Micro soft. CustomProviders/Associations en micro soft. CustomProviders/resourceProviders hoeven geen deel uit te maken van dezelfde resource groep.

Hier volgt een voor beeld van een aangepaste Azure-provider:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Eigenschap | Vereist? | Beschrijving
---|---|---
naam | Ja | De naam van de eindpuntdefinitie. Voor het onboarden van resources moet de naam ' Associations ' zijn.
routingType | Ja | Bepaalt het type contract met het eind punt. Voor onboarding van resources zijn de geldige **routingTypes** ' proxy, cache, extensie ' en ' webhook, cache, extensie '.
endpoint | Ja | Het eindpunt waarnaar de aanvragen worden gerouteerd. Hiermee worden de reactie en eventuele neven effecten van de aanvraag verwerkt.

Nadat u de aangepaste provider met het resource type voor koppelingen hebt gemaakt, kunt u een target gebruiken met behulp van micro soft. CustomProviders/Associations. Micro soft. CustomProviders/Associations is een extensie resource waarmee elke andere Azure-resource kan worden uitgebreid. Wanneer een exemplaar van micro soft. CustomProviders/Associations wordt gemaakt, wordt er een eigenschap **targetResourceId**, die een geldige resource-id van micro soft. CustomProviders/ResourceProviders of micro soft. Solutions/Applications moet zijn. In deze gevallen wordt de aanvraag doorgestuurd naar het resource type van de koppeling voor het micro soft. CustomProviders/resourceProviders-exemplaar dat u hebt gemaakt.

> [!NOTE]
> Als de resource-ID van micro soft. Solutions/toepassingen is ingesteld als **targetResourceId**, moet er een micro soft. CustomProviders/resourceProviders zijn geïmplementeerd in de beheerde resource groep met de naam ' Public '.

Voor beeld van een Azure Custom providers-koppeling:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Eigenschap | Vereist? | Beschrijving
---|---|---
targetResourceId | Ja | De resource-ID van micro soft. CustomProviders/resourceProviders of micro soft. Solutions/Applications.

## <a name="how-to-use-resource-onboarding"></a>Het gebruik van resource-onboarding

Het onboarden van resources werkt door andere resources uit te breiden met de extensie resource micro soft. CustomProviders/Associations. In het volgende voor beeld wordt de aanvraag ingediend voor een virtuele machine, maar een resource kan worden uitgebreid.

Eerst moet u een aangepaste provider resource maken met het resource type van een koppeling. Hiermee wordt de call back-URL gedeclareerd die wordt gebruikt wanneer een bijbehorende micro soft. CustomProviders/Associations-resource wordt gemaakt, die gericht is op de aangepaste provider.

Voor beeld van micro soft. CustomProviders/resourceProviders Create aanvraag:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

Nadat u de aangepaste provider hebt gemaakt, kunt u andere resources richten en de neven effecten van de aangepaste provider hierop Toep assen.

Voor beeld van micro soft. CustomProviders/Associations-aanvraag maken:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Deze aanvraag wordt vervolgens doorgestuurd naar het eind punt dat is opgegeven in de aangepaste provider die u hebt gemaakt, waarnaar wordt verwezen door de **targetResourceId** in dit formulier:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Het eind punt moet reageren met een toepassing/JSON `Content-Type` en een geldige JSON-antwoord tekst. Velden die worden geretourneerd onder het object **Properties** van de JSON, worden toegevoegd aan de Association retour-antwoord.

## <a name="getting-help"></a>Ondersteuning vragen

Als u vragen hebt over het ontwikkelen van aangepaste Azure-resource providers, vraagt u deze op [stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Misschien is een soortgelijke vraag al eerder gesteld en beantwoord, dus controleer dit eerst vóór u een vraag stelt. Voeg de tag ```azure-custom-providers``` toe om snel antwoord te krijgen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over aangepaste providers. Zie voor meer informatie de volgende artikelen:

- [Zelf studie: onboarding van resources met aangepaste providers](./tutorial-resource-onboarding.md)
- [Zelf studie: aangepaste acties en resources maken in azure](./tutorial-get-started-with-custom-providers.md)
- [Snelstartgids: een aangepaste resource provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Procedure: aangepaste acties toevoegen aan een Azure-REST API](./custom-providers-action-endpoint-how-to.md)
- [Procedure: aangepaste resources toevoegen aan een Azure-REST API](./custom-providers-resources-endpoint-how-to.md)
