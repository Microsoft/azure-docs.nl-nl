---
title: OptionsGroup UI-element
description: Hierin wordt het micro soft. common. OptionsGroup UI-element voor Azure Portal beschreven. Hiermee kunnen gebruikers kiezen uit beschik bare opties bij het implementeren van een beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: aa73b4cbded98291a14792a7151df9fdfb885b53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87004184"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Micro soft. common. OptionsGroup UI-element

Met het besturings element OptionsGroup kunnen gebruikers één optie selecteren uit twee of meer keuzes. Een gebruiker kan slechts één optie selecteren.

> [!NOTE]
> In het verleden heeft dit besturings element de opties horizon taal gerenderd. Het besturings element bevat nu de opties verticaal als keuze rondjes.

## <a name="ui-sample"></a>UI-voor beeld

:::image type="content" source="./media/managed-application-elements/microsoft-common-optionsgroup-2.png" alt-text="Microsoft.Common.OptionsGroup":::

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"two"
```

## <a name="remarks"></a>Opmerkingen

- Het label voor `constraints.allowedValues` is de weergave tekst voor een item en de waarde is de uitvoer waarde van het element wanneer dit is geselecteerd.
- Indien opgegeven, moet de standaard waarde een label zijn in `constraints.allowedValues` . Als u niets opgeeft, wordt het eerste item in `constraints.allowedValues` standaard geselecteerd. De standaard waarde is **Null**.
- `constraints.allowedValues` moet ten minste één item bevatten.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
