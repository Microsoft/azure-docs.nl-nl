---
title: 'Patroon: Tags gebruiken in een beleidsdefinitie'
description: Dit Azure Policy-patroon biedt voorbeelden van hoe u tags met een parameter kunt toevoegen aan, of tags kunt overnemen van een resourcegroep in een beleidsdefinitie.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: c748eb9b8ea795f9725082ec0aa0b8065ada8c65
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093364"
---
# <a name="azure-policy-pattern-tags"></a>Azure Policy-patroon: tags

[Tags](../../..//azure-resource-manager/management/tag-resources.md) zijn een belangrijk onderdeel van het beheren en ordenen van uw Azure-resources. Met Azure Policy kunt u op grote schaal tags configureren voor uw nieuwe en bestaande resources voor het effect [aanpassen](../concepts/effects.md#modify) en [hersteltaken](../how-to/remediate-resources.md).

## <a name="sample-1-parameterize-tags"></a>Voorbeeld 1: Parameters toevoegen aan tags

Deze beleidsdefinitie gebruikt twee parameters, **tagName** en **tagValue**, om in te stellen waar de beleidstoewijzing naar zoekt bij resourcegroepen. Dankzij deze indeling kan de beleidsdefinitie worden gebruikt voor alle combinaties van tagnaam en tagwaarde, met behoud van één beleidsdefinitie.

> [!NOTE]
> Hoewel dit beleidsdefinitiepatroon vergelijkbaar is met dat in [Patroon: Parameters - voorbeeld 1](./pattern-parameters.md#sample-1-string-parameters), worden in dit voorbeeld **modus** _Alle_ gebruikt en zijn alle resourcegroep het doel.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>Voorbeeld 1: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

In dit voorbeeld is **modus** ingesteld op _Alle_ omdat het een resourcegroep als doel heeft. In de meeste gevallen moet **modus** zijn ingesteld op _Geïndexeerd_ wanneer u met tags werkt. Zie [modi](../concepts/definition-structure.md#resource-manager-modes) voor meer informatie.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

In dit gedeelte van de beleidsdefinitie combineert `concat` de parameter **tagName** en de `tags['name']`-indeling om **veld** die tag te laten evalueren voor de parameter **tagValue**.
Omdat **notEquals** wordt gebruikt, wordt het effect **aanpassen**  getriggerd als **tags\[tagName\]** niet overeenkomt met **tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

Hier wordt de indeling voor het gebruik van tagwaarden met parameters ook gebruikt voor de bewerking **addOrReplace**, voor het maken of bijwerken van de tag naar de gewenste waarde voor de geëvalueerde resourcegroep.

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>Voorbeeld 2: Tagwaarde overnemen uit resourcegroep

Deze beleidsdefinitie gebruikt de parameter **tagName** om te bepalen van welke tag de waarde moet worden overgenomen uit de bovenliggende resourcegroep.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>Voorbeeld 2: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

In dit voorbeeld is **modus** ingesteld op _Geïndexeerd_ omdat het doel geen resourcegroep of abonnement is, ondanks dat de waarde afkomstig is uit een resourcegroep. Zie [modi](../concepts/definition-structure.md#resource-manager-modes) voor meer informatie.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

De **policyRule.if** gebruikt `concat` zoals [Voorbeeld 1](#sample-1-parameterize-tags) om de waarde van **tagName** te evalueren maar gebruikt de functie `resourceGroup()` om die te vergelijken met de waarde van dezelfde tag van de bovenliggende resourcegroep. Het tweede component controleert of de tag van de resourcegroep een waarde heeft en niet null is.

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

Hier gebruikt de waarde die wordt toegewezen aan de tag **tagName** van de resource ook de functie `resourceGroup()` om de waarde op te halen van de bovenliggende resourcegroep. Op die manier kunt u tags overnemen van bovenliggende resourcegroepen. Als u de resource al hebt gemaakt maar de tag niet hebt toegevoegd, kunt u met dezelfde beleidsdefinitie en een [hersteltaak](../how-to/remediate-resources.md) bestaande resources bijwerken.

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).