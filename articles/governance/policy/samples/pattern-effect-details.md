---
title: 'Patroon: Effecten van een beleidsdefinitie'
description: Dit Azure Policy-patroon biedt een voorbeeld van het gebruik van de verschillende effecten van een beleidsdefinitie.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: f1da9bd153707db35c07ed3c176542797a694d7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92073033"
---
# <a name="azure-policy-pattern-effects"></a>Azure Policy-patroon: effecten

In Azure Policy zijn een aantal [effecten](../concepts/effects.md) beschikbaar waarmee u kunt bepalen hoe de service om niet-compatibele resources reageert. Een aantal effecten is eenvoudig; hiervoor zijn geen aanvullende eigenschappen in de beleidsdefinitie vereist. Voor andere effecten zijn verschillende eigenschappen vereist.

## <a name="sample-1-simple-effect"></a>Voorbeeld 1: Eenvoudig effect

Met deze beleidsdefinitie controleert u of de tag die in de parameter **tagName** is gedefinieerd, bestaat in de geëvalueerde resource. Als de tag nog niet bestaat, wordt het effect [aanpassen](../concepts/effects.md#modify) geactiveerd om de tag met de waarde in de parameter **tagValue** toe te voegen.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Voorbeeld 1: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

Voor het effect **aanpassen** is het **policyRule.then.details**-blok vereist waarmee **roleDefinitionIds** en **operations** worden gedefinieerd. Aan de hand van deze parameters weet Azure Policy welke rollen nodig zijn om de tag toe te voegen en de resource te herstellen en welke bewerking voor **aanpassen** moet worden gebruikt. In dit voorbeeld worden de _bewerking_ **add** en de parameters gebruikt om de tag en de waarde daarvan in te stellen.

## <a name="sample-2-complex-effect"></a>Voorbeeld 2: Complex effect

Met deze beleidsdefinitie wordt elke virtuele machine gecontroleerd wanneer een extensie, die in de parameters **publisher** en **type** is gedefinieerd, niet bestaat. [auditIfNotExists](../concepts/effects.md#auditifnotexists) wordt gebruikt om een resource met betrekking tot de virtuele machine te controleren om te zien of er een exemplaar bestaat dat met de gedefinieerde parameters overeenkomt. In dit voorbeeld wordt het type **extensions** gecontroleerd.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Voorbeeld 2: Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Voor het effect **auditIfNotExists** is vereist dat in het **policyRule.then.details**-blok zowel een **type** als de **existenceCondition** waarnaar moet worden gezocht, wordt gedefinieerd. Voor de **existenceCondition** worden elementen uit de beleidstaal gebruikt, zoals [logische operators](../concepts/definition-structure.md#logical-operators), om te bepalen of er een overeenkomstige resource bestaat. In dit voorbeeld zijn de waarden die bij elke [alias](../concepts/definition-structure.md#aliases) zijn gecontroleerd, gedefinieerd in parameters.

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).