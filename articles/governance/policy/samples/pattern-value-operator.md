---
title: 'Patroon: De waarde-operator in een beleidsdefinitie'
description: Dit Azure Policy-patroon biedt een voorbeeld van het gebruik van de waarde-operator in een beleidsdefinitie.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 8392c69ff3d63ff4ecad2a26d5d914b4766147b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072880"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure Policy-patroon: de waarde-operator

De [waarde](../concepts/definition-structure.md#value)-operator evalueert [parameters](../concepts/definition-structure.md#parameters), [ondersteunde sjabloonfuncties](../concepts/definition-structure.md#policy-functions), of letterlijke waarde voor een opgegeven waarde voor een bepaalde [voorwaarde](../concepts/definition-structure.md#conditions).

> [!WARNING]
> Als het resultaat van een _sjabloonfunctie_ een fout is, mislukt de beleidsevaluatie. Een mislukte evaluatie is een impliciete **weigering**. Zie [sjabloonfouten vermijden](../concepts/definition-structure.md#avoiding-template-failures) voor meer informatie.

## <a name="sample-policy-definition"></a>Voorbeeld van beleidsdefinitie

Met deze beleidsdefinitie wordt het label dat is opgegeven in de parameter **tagName** (_string_) op resources toegevoegd of vervangen en wordt de waarde voor **tagName** overgenomen van de resourcegroep waarin de resource zich bevindt. Deze evaluatie gebeurt wanneer de resource wordt gemaakt of bijgewerkt. Als effect [wijzigen](../concepts/effects.md#modify) kan het herstel worden uitgevoerd voor bestaande resources via een [hersteltaak](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

De operator **waarde** wordt gebruikt in het blok **policyRule.if** binnen **eigenschappen**. In dit voorbeeld wordt de [logische operator](../concepts/definition-structure.md#logical-operators) **allOf** gebruikt om te controleren of aan beide voorwaarden voldaan moet worden opdat het effect, **wijzigen** kan plaatsvinden.

**waarde** evalueert het resultaat van de sjabloonfunctie [resourceGroup ()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) voor de voorwaarde **notEquals** van een lege waarde. Als de labelnaam opgegeven in **tagName** voor de bovenliggende resourcegroep bestaat, dan wordt de voorwaarde als waar beschouwd.

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).