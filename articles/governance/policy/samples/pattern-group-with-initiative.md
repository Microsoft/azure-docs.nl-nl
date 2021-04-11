---
title: 'Patroon: Beleidsdefinities groeperen met initiatieven'
description: Dit Azure Policy-patroon laat zien hoe u beleidsdefinities kunt groeperen in een initiatief.
ms.date: 03/31/2021
ms.topic: sample
ms.openlocfilehash: 7bbb2efdd27ead942fa0ef48f7785eec8bce9378
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092854"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure Policy-patroon: beleidsdefinities groeperen

Een initiatief is een groep beleidsdefinities. Door verwante beleidsdefinities in één object te groeperen, kunt u één toewijzing maken in de plaats van meerdere.

## <a name="sample-initiative-definition"></a>Voorbeeld van initiatiefdefinitie

Dit initiatief implementeert twee beleidsdefinities die allebeide de parameters **tagName** en **tagValue** overnemen. Het initiatief zelf heeft twee parameters: **costCenterValue** en **productNameValue**.
Deze initiatiefparameters worden beide gegeven aan elk van de beleidsdefinities in de groep. Dit ontwerp hergebruikt zoveel mogelijk bestaande beleidsdefinities en beperkt het aantal toewijzingen dat wordt gemaakt voor de implementatie.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Uitleg

#### <a name="initiative-parameters"></a>Initiatiefparameters

Een initiatief kan zijn eigen parameters definiëren, die vervolgens worden doorgegeven aan de beleidsdefinities in de groep.
In dit voorbeeld worden zowel **costCenterValue** als **productNameValue** gedefinieerd als parameters van het initiatief. De waarden worden opgegeven bij toewijzing van het initiatief.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Inclusief beleidsdefinities

Elke opgenomen beleidsdefinitie moet de **policyDefinitionId** en een matrix met **parameters** opgeven als de beleidsdefinitie parameters accepteert. In het onderstaande fragment heeft de opgenomen beleidsdefinitie twee parameters: **tagName** en **tagValue**. **tagName** wordt gedefinieerd met een letterlijke waarde, maar **tagValue** gebruikt de parameter **costCenterValue** die gedefinieerd wordt door het initiatief. Deze passthrough van waarden zorgt voor beter hergebruik.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).