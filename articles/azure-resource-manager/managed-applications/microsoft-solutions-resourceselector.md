---
title: ResourceSelector UI-element
description: Hierin wordt het UI-element micro soft. Solutions. ResourceSelector voor Azure Portal beschreven. Wordt gebruikt voor het ophalen van een lijst met bestaande resources.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: d4b21f092733f6ebb3c2bf5aa06c1d2782323730
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87097275"
---
# <a name="microsoftsolutionsresourceselector-ui-element"></a>Micro soft. Solutions. ResourceSelector UI-element

Met ResourceSelector kunnen gebruikers een bestaande resource uit een abonnement selecteren.

## <a name="ui-sample"></a>UI-voor beeld

![Micro soft. Solutions. ResourceSelector](./media/managed-application-elements/microsoft-solutions-resourceselector.png)

## <a name="schema"></a>Schema

```json
{
    "name": "storageSelector",
    "type": "Microsoft.Solutions.ResourceSelector",
    "label": "Select storage accounts",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "options": {
        "filter": {
            "subscription": "onBasics",
            "location": "onBasics"
        }
    }
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"name": "{resource-name}",
"id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}",
"location": "{deployed-location}"
```

## <a name="remarks"></a>Opmerkingen

Geef in de `resourceType` eigenschap de naam ruimte van de resource provider en het resource type op voor de resource die u wilt weer geven in de lijst.

De `filter` eigenschap beperkt de beschik bare opties voor de resources. U kunt de resultaten beperken op locatie of abonnement. Als u alleen resources wilt weer geven die overeenkomen met de selectie in de basis beginselen, gebruikt u `onBasics` . Gebruik om alle resources weer te geven `all` . De standaardwaarde is `all`.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
