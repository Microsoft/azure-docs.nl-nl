---
title: Meer informatie over de volg orde van de implementatie volgorde
description: Meer informatie over de standaard volgorde waarmee blauw drukken artefacten worden geïmplementeerd tijdens een blauw druk-toewijzing en het aanpassen van de implementatie volgorde.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: ada7e7771e44e6f43c8546e8307fd5d2b218c009
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918225"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Meer informatie over de implementatie volgorde in azure-blauw drukken

Azure-blauw drukken maakt gebruik van een **sequentiëren** voor het bepalen van de volg orde van het maken van resources bij het verwerken van de toewijzing van een definitie van een blauw druk. In dit artikel worden de volgende concepten uitgelegd:

- De standaard volgorde voor sequentiëren die wordt gebruikt
- De volg orde aanpassen
- Hoe de aangepaste volg orde wordt verwerkt

De JSON-voor beelden bevatten variabelen die u moet vervangen door uw eigen waarden:

- Vervang `{YourMG}` door de naam van uw beheergroep

## <a name="default-sequencing-order"></a>Standaard volgorde voor sequentiëren

Als de definitie van de blauw druk geen instructie bevat voor het implementeren van artefacten of als de instructie null is, wordt de volgende volg orde gebruikt:

- Artefact niveau **roltoewijzingen van rol** , gesorteerd op artefact naam
- **Beleids toewijzings** artefacten op abonnements niveau gesorteerd op artefact naam
- **Azure Resource Manager sjabloon** op abonnements niveau (arm-sjablonen) artefacten gesorteerd op artefact naam
- **Bron groeps** artefacten (inclusief onderliggende artefacten) gesorteerd op naam van tijdelijke aanduiding

Binnen elk **bron groeps** artefact wordt de volgende volg orde gebruikt voor artefacten die in die resource groep moeten worden gemaakt:

- Artefacten van onderliggende **roltoewijzingen** van resource groep gesorteerd op artefact naam
- Artefacten van onderliggende **beleids toewijzing** van resource groep gesorteerd op artefact naam
- Onderliggend **Azure Resource Manager sjabloon** van resource groep (arm-sjablonen) artefacten gesorteerd op artefact naam

> [!NOTE]
> Het gebruik van [artefacten ()](../reference/blueprint-functions.md#artifacts) maakt een impliciete afhankelijkheid van het artefact waarnaar wordt verwezen.

## <a name="customizing-the-sequencing-order"></a>De volg orde van sequentiëren aanpassen

Bij het opstellen van grote blauw drukken-definities kan het nodig zijn om resources in een specifieke volg orde te maken. Het meest voorkomende patroon van dit scenario is wanneer een blauw druk definitie meerdere ARM-sjablonen bevat. Met Azure-blauw drukken wordt dit patroon verwerkt door de volg orde van sequentiëren te definiëren.

De volg orde wordt bereikt door een `dependsOn` eigenschap in de JSON te definiëren. De definitie van de blauw druk, voor resource groepen en artefact objecten ondersteunen deze eigenschap. `dependsOn` is een teken reeks matrix van artefact namen waarvan het specifieke artefact moet worden gemaakt voordat deze wordt gemaakt.

> [!NOTE]
> Wanneer u blauw drukken-objecten maakt, haalt elke artefact bron de naam van de bestands naam, indien [Power shell](/powershell/module/az.blueprint/new-azblueprintartifact)of het URL-eind punt wordt gebruikt als [rest API](/rest/api/blueprints/artifacts/createorupdate). _resourceGroup_ verwijzingen in artefacten moeten overeenkomen met die in de definitie van de blauw druk.

### <a name="example---ordered-resource-group"></a>Voor beeld-bestelde resource groep

Deze voor beeld-blauw druk definitie bevat een resource groep waarvoor een aangepaste volgorde voor volgorde bepaling is gedefinieerd door een waarde voor te declareren `dependsOn` , samen met een standaard resource groep. In dit geval wordt het artefact met de naam **assignPolicyTags** verwerkt vóór de **bestelde-RG-** resource groep.
**Standard-RG** wordt verwerkt volgens de standaard volgorde voor sequentiëren.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Voor beeld-artefact met aangepaste volg orde

Dit voor beeld is een beleids artefact dat afhankelijk is van een ARM-sjabloon. Standaard wordt een beleids artefact gemaakt vóór de ARM-sjabloon. Deze volg orde zorgt ervoor dat de beleids artefact kan wachten tot de ARM-sjabloon is gemaakt.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Voor beeld: sjabloon artefact op abonnements niveau, afhankelijk van een resource groep

Dit voor beeld is voor een ARM-sjabloon die is geïmplementeerd op abonnements niveau, afhankelijk is van een resource groep. In de standaard volgorde worden de artefacten van het abonnements niveau gemaakt vóór alle resource groepen en onderliggende artefacten in deze resource groepen. De resource groep wordt in de definitie van de blauw druk als volgt gedefinieerd:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Het sjabloon artefact op abonnements niveau, afhankelijk van de resource groep **wacht op mijzelf** , is als volgt gedefinieerd:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>De aangepaste volg orde verwerken

Tijdens het maken wordt een topologische sortering gebruikt voor het maken van de afhankelijkheids grafiek van de blauw drukken-artefacten. De controle zorgt ervoor dat elk niveau van afhankelijkheid tussen resource groepen en artefacten wordt ondersteund.

Als een artefact afhankelijkheid is gedeclareerd en de standaard volgorde niet zou wijzigen, wordt er geen wijziging aangebracht.
Een voor beeld is een resource groep die afhankelijk is van een beleid op abonnements niveau. Een ander voor beeld is een onderliggende beleids toewijzing van een resource groep, die afhankelijk is van de resource groep Standard-RG, de toewijzing van onderliggende rollen. In beide gevallen is de `dependsOn` standaard volgorde voor sequentiëren niet gewijzigd en worden er geen wijzigingen aangebracht.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van een blauwdruk](./lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](./parameters.md) gebruikt.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](./resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](../troubleshoot/general.md).