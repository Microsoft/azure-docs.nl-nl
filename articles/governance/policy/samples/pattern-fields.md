---
title: 'Patroon: Veldeigenschappen in een beleidsdefinitie'
description: Dit Azure Policy-patroon biedt een voorbeeld van het gebruik van veldeigenschappen in een beleidsdefinitie.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 267c687f78f0bbb100843faee40ab6f3d3cbb64c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92072965"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure Policy-patroon: veldeigenschappen

De operator [veld](../concepts/definition-structure.md#fields) evalueert de opgegeven eigenschap of [alias](../concepts/definition-structure.md#aliases) voor een waarde voor een bepaalde [voorwaarde](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Voorbeeld van beleidsdefinitie

Met deze beleidsdefinitie kunt u toegestane regio's vastleggen die voldoen aan de geografische vereisten van uw organisatie. De toegestane resources worden gedefinieerd in de parameter **listOfAllowedLocations** (_matrix_). Resources die voldoen aan de definitie worden [geweigerd](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Uitleg

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

De operator **veld** wordt drie keer gebruikt binnen de [logische operator](../concepts/definition-structure.md#logical-operators) **allOf**.

- Het eerste gebruik evalueert de `location`-eigenschap met de voorwaarde **notIn** voor de parameter **listOfAllowedLocations**. **notIn** werkt aangezien het een _matrix_ verwacht en de parameter een _matrix_ is. Als de `location` van de gemaakte of bijgewerkte resource zich niet in de goedgekeurde lijst bevindt, wordt dit element als waar beoordeeld.
- Het tweede gebruik evalueert ook de `location`-eigenschap, maar gebruikt de voorwaarde **notEquals** om te zien of de resource _globaal_ is. Als de `location` van de gemaakte of bijgewerkte resource niet _globaal_ is, wordt dit element als waar beoordeeld.
- Het laatste gebruik evalueert de `type`-eigenschap en gebruikt de voorwaarde **notEquals** om te controleren dat het resourcetype niet _Microsoft.AzureActiveDirectory/b2cDirectories_ is. Als dat niet het geval is, wordt dit element als waar beoordeeld.

Als aan alle drie voorwaarden in de logische operator **allOf** voldaan wordt, dan wordt het maken of bijwerken van de resource geblokkeerd door Azure Policy.

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).