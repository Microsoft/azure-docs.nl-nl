---
title: InfoBox UI-element
description: Hierin wordt het micro soft. common. InfoBox UI-element voor Azure Portal beschreven. Gebruiken om tekst of waarschuwingen toe te voegen bij het implementeren van een beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 7580ac0650706d6aee49bbf0e8235e8c5dab33f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87033344"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Micro soft. common. InfoBox UI-element

Een besturings element waarmee een informatie venster wordt toegevoegd. Het vak bevat belang rijke tekst of waarschuwingen waarmee gebruikers inzicht kunnen krijgen in de waarden die ze bieden. Het kan ook worden gekoppeld aan een URI voor meer informatie.

## <a name="ui-sample"></a>UI-voor beeld

![Micro soft. common. InfoBox](./media/managed-application-elements/microsoft-common-infobox.png)


## <a name="schema"></a>Schema

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Opmerkingen

* Voor `icon` gebruikt u **geen**, **info**, **waarschuwing** of **fout**.
* De `uri` eigenschap is optioneel.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
