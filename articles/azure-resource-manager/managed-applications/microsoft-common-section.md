---
title: UI-element van sectie
description: Hierin wordt het gebruikers interface-element micro soft. common. Section voor Azure Portal beschreven. Gebruiken om elementen in de portal te groeperen voor het implementeren van beheerde toepassingen.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 924aff8f2ba3d796b65f52494845f3b10018065c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063977"
---
# <a name="microsoftcommonsection-ui-element"></a>Gebruikers interface-element van micro soft. common. Section

Een besturings element dat een of meer elementen onder een kop groepeert.

## <a name="ui-sample"></a>UI-voor beeld

![Microsoft.Common.Section](./media/managed-application-elements/microsoft-common-section.png)

## <a name="schema"></a>Schema

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Opmerkingen

- `elements` moet ten minste één element hebben en kan alle element typen behalve bevatten `Microsoft.Common.Section` .
- Dit element biedt geen ondersteuning voor de `toolTip` eigenschap.

## <a name="sample-output"></a>Voorbeelduitvoer
Als u de uitvoer waarden van elementen in wilt openen `elements` , gebruikt u de functies [()](create-ui-definition-referencing-functions.md#basics) of [stappen (](create-ui-definition-referencing-functions.md#steps) ) en punt notatie:

```json
steps('configuration').section1.text1
```

Elementen van het type `Microsoft.Common.Section` hebben geen uitvoer waarden zelf.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
