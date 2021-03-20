---
title: Gebruikers interface-element schuif regelaar
description: Hierin wordt het UI-element micro soft. common. Slider voor Azure Portal beschreven. Hiermee kunnen gebruikers een waarde instellen op basis van een reeks opties.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87097100"
---
# <a name="microsoftcommonslider-ui-element"></a>Micro soft. common. Slider UI-element

Met het besturings element schuif regelaar kunnen gebruikers een bereik van toegestane waarden selecteren.

## <a name="ui-sample"></a>UI-voor beeld

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="Micro soft. common. Slider":::

## <a name="schema"></a>Schema

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
26
```

## <a name="remarks"></a>Opmerkingen

- De `min` `max` waarden en zijn vereist. Ze stellen het begin-en eind punt in voor de schuif regelaar.
- De `showStepMarkers` eigenschap wordt standaard ingesteld op True. De stap markeringen worden alleen weer gegeven als het bereik van min tot maximum 100 of minder is.


## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
