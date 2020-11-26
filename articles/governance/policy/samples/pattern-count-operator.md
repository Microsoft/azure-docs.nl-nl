---
title: 'Patroon: De count-operator in een beleidsdefinitie'
description: Dit Azure Policy-patroon biedt een voorbeeld van het gebruik van de count-operator in een beleidsdefinitie.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 1339dff7f8bc92a8e38ec5635690cc2069dd8df4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005415"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Azure Policy-patroon: de count-operator

De [count](../concepts/definition-structure.md#count)-operator evalueert leden van een \[\*\]-alias.

## <a name="sample-policy-definition"></a>Voorbeeld van beleidsdefinitie

Deze beleidsdefinitie [controleert](../concepts/effects.md#audit) netwerkbeveiligingsgroepen die zijn geconfigureerd om inkomend Remote Desktop Protocol (RDP)-verkeer toe te staan.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Uitleg

De belangrijkste onderdelen van de **count**-operator zijn _field_, _where_ en de voorwaarde. Elk is gemarkeerd in het onderstaande fragment.

- In _veld_ wordt aangegeven welke [alias](../concepts/definition-structure.md#aliases) wordt gebruikt om leden te evalueren. Hier bekijken we de **securityRules\[\*\]** -alias _matrix_ van de netwerkbeveiligingsgroep.
- _where_ gebruikt de beleidstaal om te definiëren welke _matrix_-leden voldoen aan de criteria. In dit voor beeld groepeert een logische operator **allOf** drie verschillende voorwaarden van de alias _matrix_-eigenschappen: _direction_, _access_ en _destinationPortRange_.
- De telvoorwaarde in dit voorbeeld is **meer**. Count evalueert als True wanneer een of meer leden van de alias _matrix_ overeenkomt met het component _where_.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Volgende stappen

- Bekijk andere [patronen en ingebouwde definities](./index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).