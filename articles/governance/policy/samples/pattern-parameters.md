---
title: 'Patroon: Parameters in een beleidsdefinitie'
description: Dit Azure Policy patroon bevat een voor beeld van hoe u teken reeks-en matrix parameters kunt gebruiken in een beleids definitie en hoe u het effect para meters.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: b742aaaf950e2b5670edbaa1f0134da144e675b6
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092769"
---
# <a name="azure-policy-pattern-parameters"></a>Azure Policy-patroon: parameters

Een beleidsdefinitie kan via [parameters](../concepts/definition-structure.md#parameters) dynamisch worden gemaakt om het aantal beleidsdefinities te beperken dat nodig is. De parameter wordt gedefinieerd tijdens de beleidstoewijzing. Parameters hebben een aantal vooraf gedefinieerde eigenschappen die de parameter en de manier waarop deze worden gebruikt, beschrijven.

## <a name="sample-1-string-parameters"></a>Voorbeeld 1: Tekenreeksparameters

Deze beleids definitie maakt gebruik van twee para meters, **tagName** en **tagValue**, om in te stellen wat de beleids toewijzing op zoek is naar bronnen. Dankzij deze indeling kan de beleidsdefinitie worden gebruikt voor alle combinaties van tagnaam en tagwaarde, met behoud van één beleidsdefinitie.

> [!NOTE]
> Als u een voorbeeld met een tag wilt zien waarin **mode** _All_ wordt gebruikt en dat gebruikmaakt van een resourcegroep, gaat u naar [Patroon: Tags - Voorbeeld 1](./pattern-tags.md#sample-1-parameterize-tags).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Voorbeeld 1: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

In dit gedeelte van de beleidsdefinitie wordt de parameter **tagName** gedefinieerd als een _string_ (tekenreeks), met inbegrip van een beschrijving voor het gebruik van de parameter.

De parameter wordt vervolgens gebruikt in het blok **policyRule.if** om het beleid dynamisch te maken. Hier wordt het gebruikt om het veld te definiëren dat wordt geëvalueerd. Dit is een tag met de waarde **tagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Voorbeeld 2: Matrixparameters

In deze beleidsdefinitie wordt één parameter gebruikt, **listOfBandwidthinMbps**, om te controleren of voor de Express Route Circuit-resource de bandbreedte-instelling is geconfigureerd op een van de goedgekeurde waarden. Als dat niet zo is, wordt het maken of bijwerken van de resource [geweigerd](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Voorbeeld 2: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

In dit gedeelte van de beleidsdefinitie wordt de parameter **listOfBandwidthinMbps** gedefinieerd als een _array_ (matrix), met inbegrip van een beschrijving voor het gebruik van de parameter. Als een _array_ zijn er meerdere waarden die moeten overeenkomen.

De parameter wordt vervolgens gebruikt in het blok **policyRule.if**. Als een _array_-parameter, moet de 
[voorwaarde](../concepts/definition-structure.md#conditions) **in** of **notIn** van _array_ worden gebruikt.
Hier wordt de parameter gebruikt met de alias **serviceProvider.bandwidthInMbps** als een van de gedefinieerde waarden.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="sample-3-parameterized-effect"></a>Voorbeeld 3: Geparameteriseerd effect

Een veelgebruikte manier om beleidsdefinities geschikt te maken voor hergebruik, is om het effect zelf te parameteriseren. In dit voorbeeld wordt slechts één parameter gebruikt, **effect**. Door het effect te parameteriseren, is het mogelijk om dezelfde definitie toe te wijzen aan verschillende bereiken met verschillende effecten.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json":::

### <a name="sample-3-explanation"></a>Voorbeeld 3: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="11-25":::

In dit gedeelte van de beleidsdefinitie wordt de parameter **effect** gedefinieerd als _string_. Met de beleidsdefinitie wordt de standaardwaarde voor een toewijzing ingesteld op _audit_ en worden de andere opties beperkt tot _disabled_ en _deny_.

De parameter wordt vervolgens gebruikt in het blok **policyRule.then** voor het _effect_.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="38-40" highlight="2":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).