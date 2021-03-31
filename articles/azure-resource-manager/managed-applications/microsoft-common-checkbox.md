---
title: UI-element CheckBox
description: Hierin wordt het gebruikers interface-element micro soft. common. CheckBox voor Azure Portal beschreven. Hiermee kunnen gebruikers ervoor kiezen om een optie te controleren of uit te checken.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87097104"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Gebruikers interface-element van micro soft. common. CheckBox

Met het besturings element selectie vakje kunnen gebruikers een optie controleren of uitschakelen. Het besturings element retourneert **waar** wanneer het besturings element is ingecheckt of **Onwaar** wanneer het niet is ingeschakeld.

## <a name="ui-sample"></a>UI-voor beeld

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Micro soft. common. CheckBox":::

## <a name="schema"></a>Schema

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
true
```

## <a name="remarks"></a>Opmerkingen

Wanneer u **vereist** instelt op **waar**, moet de gebruiker het selectie vakje inschakelen. Als de gebruiker het selectie vakje niet inschakelt, wordt het validatie bericht weer gegeven.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
