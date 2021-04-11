---
title: 'Patroon: Resources implementeren met een beleidsdefinitie'
description: Dit Azure Policy-patroon biedt een voorbeeld van het implementeren van resources met de beleidsdefinitie deployIfNotExists.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: 1dda3d5f2d4bef57919931ec3c1635310723e0c8
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093397"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Azure Policy-patroon: resources implementeren

Met het effect [deployIfNotExists](../concepts/effects.md#deployifnotexists) kunt u een [ARM-sjabloon](../../../azure-resource-manager/templates/overview.md) (Azure Resource Manager) implementeren wanneer u een resource maakt of bijwerkt die niet compatibel is. U kunt de voorkeur geven aan deze methode, boven het gebruik van het effect [weigeren](../concepts/effects.md#deny), omdat er nog steeds resources worden gemaakt, maar de wijzigingen in deze resources altijd compatibel zijn.

## <a name="sample-policy-definition"></a>Voorbeeld van beleidsdefinitie

Deze beleidsdefinitie maakt gebruik van de operator **veld** om de `type` van de gemaakte of bijgewerkte resource te evalueren. Als deze resource een _Microsoft.Network/virtualNetworks_ is, zoekt het beleid naar een network watcher op de locatie van de nieuwe of bijgewerkte resource. Als er geen overeenkomende network watcher is gevonden, wordt de ARM-sjabloon geïmplementeerd om de ontbrekende resource te maken.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Uitleg

#### <a name="existencecondition"></a>existenceCondition

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

Het blok **properties.policyRule.then.details** geeft aan waar met Azure Policy naar moet worden gezocht, gerelateerd aan de gemaakte of bijgewerkte resource in het blok **properties.policyRule.if**. In dit voorbeeld moet een network watcher bestaan in de resourcegroep **networkWatcherRG**, waarbij **veld** `location` gelijk is aan de locatie van de nieuwe of bijgewerkte resource. Met de functie `field()` kan **existenceCondition** toegang krijgen tot eigenschappen van de nieuwe of bijgewerkte resource, met name de eigenschap `location`.

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

De _matrix_ eigenschap **roleDefinitionIds** in het blok **properties.policyRule.then.details** geeft aan de beleidsdefinitie door welke rechten de beheerde identiteit nodig heeft om de opgenomen Resource Manager-sjabloon te implementeren. Deze eigenschap moet zijn ingesteld om rollen te omvatten die machtigingen hebben die nodig zijn voor de sjabloonimplementatie, maar moet gebruikmaken van het concept ‘principe van minste machtigingen’, en mag alleen de benodigde bewerkingen hebben en niets meer.

#### <a name="deployment-template"></a>Implementatiesjabloon

Het **implementatie** gedeelte van de beleidsdefinitie heeft een blok **eigenschappen** waarmee drie kernonderdelen worden gedefinieerd:

- **modus**: met deze eigenschap wordt de [implementatiemodus](../../../azure-resource-manager/templates/deployment-modes.md) van de sjabloon ingesteld.

- **sjabloon**: deze eigenschap bevat de sjabloon zelf. In dit voorbeeld wordt met de sjabloonparameter **locatie** de locatie van de nieuwe network watcher-resource ingesteld.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parameters**: deze eigenschap definieert parameters die worden opgegeven voor de **sjabloon**. De parameternamen moeten overeenkomen met de namen die zijn gedefinieerd voor **sjabloon**. In dit voorbeeld heet de parameter **locatie** zodat deze overeenkomt. De waarde van **locatie** maakt opnieuw gebruik van de functie `field()` om de waarde van de geëvalueerde resource op te halen, wat het virtuele netwerk in het blok **policyRule.if** is.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).